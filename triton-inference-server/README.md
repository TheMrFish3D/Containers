# NVIDIA Triton Inference Server Stack

This stack deploys NVIDIA Triton Inference Server for high-performance AI model serving and inference.

## Services

- **Triton Inference Server**: AI model serving platform for multiple frameworks
  - Port: 8000 (HTTP/REST API)
  - Port: 8001 (gRPC API)
  - Port: 8002 (Prometheus metrics)

## Features

- High-performance AI model serving
- Support for multiple ML frameworks (TensorFlow, PyTorch, ONNX, etc.)
- REST and gRPC APIs for inference
- Dynamic model loading and unloading
- Prometheus metrics for monitoring
- GPU acceleration with NVIDIA runtime
- Model repository polling for automatic updates
- Concurrent model execution
- Batching and scheduling optimizations

## Prerequisites

### System Requirements
- **GPU**: NVIDIA GPU with CUDA support required
- **Memory**: 8GB+ RAM recommended (depends on model sizes)
- **Storage**: Sufficient space for model repository
- **NVIDIA Container Toolkit**: Must be installed on host system

### NVIDIA GPU Support
1. **Install NVIDIA Container Toolkit** on the host system
2. **Verify GPU access**: Ensure Docker can access NVIDIA runtime
3. **Model Repository**: Prepare model repository with supported formats

### Directory Structure
Ensure this directory exists on your host:
- `/fileServerMountPath/data/models/` - Model repository containing your AI models

### Model Repository Structure
Organize models following Triton's expected structure:
```
/fileServerMountPath/data/models/
├── model_name_1/
│   ├── config.pbtxt
│   └── 1/
│       └── model.onnx
├── model_name_2/
│   ├── config.pbtxt
│   └── 1/
│       └── model.savedmodel/
└── model_name_3/
    ├── config.pbtxt
    └── 1/
        └── model.pt
```

## Configuration

### Environment Variables

- **Timezone**: Already configured for Brisbane:
  ```yaml
  environment:
    - TZ=Australia/Brisbane
  ```

### Optional Memory Pool Tuning
Uncomment and adjust memory pool sizes for optimization:
```yaml
environment:
  # CPU memory pool (bytes, 0 = unlimited)
  - TRITONSERVER_CPU_MEM_POOL_BYTE_SIZE=0
  # Pinned memory pool (bytes)
  - TRITONSERVER_PINNED_MEM_POOL_BYTE_SIZE=268435456
```

### Volume Configuration
Update model repository path if needed:
```yaml
volumes:
  - /your/model/path:/models
  # Optional: custom backends or logs
  - /fileServerMountPath/containers/triton:/opt/tritonserver
```

### Server Configuration
The server is configured with these optimizations:
- **Model Control Mode**: Poll mode for automatic model updates
- **Repository Poll**: 60-second intervals for model changes
- **Strict Model Config**: Disabled for flexible model loading
- **Exit on Error**: Disabled for resilient operation

## Network Access

- **REST API**: http://localhost:8000
- **gRPC API**: localhost:8001
- **Metrics**: http://localhost:8002/metrics

### API Examples

**Health Check**:
```bash
curl http://localhost:8000/v2/health/ready
```

**List Models**:
```bash
curl http://localhost:8000/v2/models
```

**Model Inference**:
```bash
curl -X POST http://localhost:8000/v2/models/your_model/infer \
  -H "Content-Type: application/json" \
  -d '{"inputs": [{"name": "input", "shape": [1, 224, 224, 3], "datatype": "FP32", "data": [...]}]}'
```

## Model Management

### Supported Frameworks
- TensorFlow (SavedModel, GraphDef)
- PyTorch (TorchScript, Python Backend)
- ONNX Runtime
- TensorRT
- OpenVINO
- Python Backend
- Custom C++ backends

### Dynamic Model Loading
Models are automatically discovered and loaded when:
1. Added to the model repository
2. Repository polling detects changes (every 60 seconds)
3. Model configuration is valid

### Model Configuration
Each model requires a `config.pbtxt` file:
```protobuf
name: "your_model"
platform: "onnxruntime_onnx"
max_batch_size: 8
input [
  {
    name: "input"
    data_type: TYPE_FP32
    dims: [ 3, 224, 224 ]
  }
]
output [
  {
    name: "output"
    data_type: TYPE_FP32
    dims: [ 1000 ]
  }
]
```

## Performance Tuning

### GPU Memory Management
Monitor GPU memory usage and adjust:
```yaml
deploy:
  resources:
    reservations:
      devices:
        - driver: nvidia
          count: 1
          capabilities: [gpu]
```

### Shared Memory
Increase shared memory for large models:
```yaml
shm_size: "4g"  # Adjust based on model requirements
```

### Concurrency and Batching
Configure in model `config.pbtxt`:
```protobuf
dynamic_batching {
  max_queue_delay_microseconds: 100
}
instance_group [
  {
    count: 2
    kind: KIND_GPU
  }
]
```

## Security Notes

- **Model Access**: Secure access to model repository directory
- **API Access**: Consider authentication for production deployments
- **Network Security**: Restrict access to inference ports if needed
- **Model Validation**: Verify model integrity before deployment
- **Monitoring**: Use Prometheus metrics for operational monitoring

## Deployment

### Start the Stack

```powershell
docker compose up -d
```

Stop the stack:

```powershell
docker compose down
```

Update the stack:

```powershell
docker compose pull
docker compose up -d
```

Ensure NVIDIA Container Toolkit is installed and model repository is prepared before deployment.

## Troubleshooting

### Common Issues

1. **GPU not available**: Verify NVIDIA Container Toolkit installation
2. **Model loading failed**: Check model format and config.pbtxt syntax
3. **Out of memory**: Reduce batch size or adjust memory pools
4. **Permission denied**: Verify model repository permissions

### Logs and Monitoring
```bash
# Triton server logs
docker logs triton

# Model status
curl http://localhost:8000/v2/models/your_model

# Server statistics
curl http://localhost:8000/v2/models/your_model/stats

# Prometheus metrics
curl http://localhost:8002/metrics
```

### Performance Analysis
```bash
# Model inference performance
perf_analyzer -m your_model -u localhost:8001 --concurrency-range 1:4

# GPU utilization
nvidia-smi dmon
```

## Backup Strategy

### Model Repository Backup
```bash
# Backup model repository
cp -r /fileServerMountPath/data/models /backup/location/models-backup-$(date +%Y%m%d-%H%M%S)

# Or create archive
tar czf /backup/triton-models-$(date +%Y%m%d-%H%M%S).tar.gz -C /fileServerMountPath/data models
```

### Configuration Backup
```bash
# Backup Triton configuration (if using custom backends)
cp -r /fileServerMountPath/containers/triton /backup/location/triton-config-$(date +%Y%m%d-%H%M%S)
```

## Links

- NGC Catalog: https://catalog.ngc.nvidia.com/orgs/nvidia/containers/tritonserver
- GitHub: https://github.com/triton-inference-server/server
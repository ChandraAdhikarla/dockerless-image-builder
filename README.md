# Dockerless Image Builder

A small utility to build Docker images **without requiring a Docker daemon or socket**. It leverages the Dockerfile-to-OCI conversion capabilities of **Kaniko** by using the Kaniko executor binary directly.

## Features
- No Docker daemon or CLI needed on the host.
- Pure user‑space image building.
- Simple CLI written in Python.
- Works on Linux/macOS (requires `tar`, `gzip` and the ability to run the Kaniko binary).

## Prerequisites
- **Python 3.8+**
- **curl** (to download the Kaniko binary) – optional; the binary is cached after first run.
- Sufficient permissions to execute the downloaded binary (chmod +x).

## Installation
```bash
git clone https://github.com/ChandraAdhikarla/dockerless-image-builder.git
cd dockerless-image-builder
pip install -r requirements.txt
```

## Usage
```bash
python build.py \
    --context PATH_TO_CONTEXT \
    --dockerfile PATH_TO_DOCKERFILE \
    --destination IMAGE_NAME[:TAG]
```
Example:
```bash
python build.py --context . --dockerfile Dockerfile --destination myrepo/myapp:latest
```
The first run will download the Kaniko executor binary (`kaniko-executor`) and store it in the project root.

## How it works
1. The script downloads the static Kaniko executor binary (if missing).
2. It creates a temporary directory with the build context packaged as a tarball.
3. Executes the Kaniko binary with flags:
   - `--dockerfile` – path to Dockerfile inside the tar.
   - `--context` – the tarball URI (`tar://`).
   - `--destination` – target registry image.
4. Kaniko builds the image and pushes it directly to the registry using the provided credentials (Docker login works as usual).

## Testing
Run the test suite with:
```bash
pytest -q
```
The tests cover argument validation and error handling.

## License
MIT License – feel free to modify and share.

# FOUND-Dataset

You must download the Habitat dataset in the directory `habitat/`, along with the additional objects that will move the scene (https://huggingface.co/datasets/ai-habitat/ycb). In this repo you can find the declarative scripts
in `scripts/`, a prompt-driven generator, and a ROS 2 runner.

## One-time setup

Clone with Git LFS so the Habitat files are downloaded:

```bash
git clone https://github.com/saraian/FOUND-Dataset.git
cd FOUND-Dataset
git lfs pull
```

Before the first commit of this repository, enable LFS for the dataset:

```bash
git lfs install
git lfs track "habitat/**"
git lfs track "*.onnx"
git add .gitattributes
```

Use the Python/Conda environment that contains Habitat-Sim and ROS 2 Python
packages. PyYAML is needed because `config.py` reads `config.yaml`:

```bash
python3 -m pip install PyYAML
```

Habitat-Sim's interactive viewer is an external dependency. Point this
variable at its `examples` directory before running the simulator node:

```bash
export HABITAT_VIEWER_EXAMPLES=/path/to/habitat-sim/examples
```

For the current machine, the value is normally:

```bash
export HABITAT_VIEWER_EXAMPLES=/root/exchange/habitat-sim/examples
```

## Configuration and credentials

`config.yaml` contains runtime configuration. By default, the dataset root is
`./habitat`; override it only when the dataset is stored elsewhere:

```bash
export HABITAT_DATASETS_DIR=/path/to/habitat
```

The batch generator requires an OpenRouter key, that must be stored in `openrouter.txt` file at the repository root:

```text
OPENROUTER_API_KEY=sk-or-v1-...
```

Alternatively, supply it for one shell session:

```bash
export OPENROUTER_API_KEY=sk-or-v1-...
```

## Run the Habitat simulator node

Activate the environment with Habitat-Sim and source the ROS 2 installation,
then run:

```bash
python3 habitat_camera_objects_node.py
```

Keep this terminal open while executing scripts from a second terminal.

## Run an existing script

List available scripts:

```bash
python3 run_habitat_script.py --list
```

Run one by its `script_id`:

```bash
python3 run_habitat_script.py <script_id>
```

The runner publishes ROS commands to the simulator node and writes transient
state/captures under `state/`; that directory is intentionally ignored.

## Generate scripts

Generate a batch across selected Habitat scenes:

```bash
python3 generate_scene_batch.py \
  --objects 10 \
  --duration-minutes 5 \
  --scene-count 3
```

To compile or inspect one plan directly:

```bash
python3 scene_script.py --help
```

Generated JSON scripts are written under `scripts/generated/`.


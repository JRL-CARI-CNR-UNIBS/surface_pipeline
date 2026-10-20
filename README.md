# surface_pipeline

Metapackage of the CARI-JRL surface pipeline for robotic surface operations (polishing, grinding, painting, inspection, ...). The pipeline acquires a surface with a wrist depth camera, reconstructs its mesh, generates a raster tool path on it and converts the path into a time-parametrized robot trajectory, exploiting the task redundancy. Every step can be driven from an Rviz panel.

This repository contains no code. It provides:

- `package.xml`: depends on all the pipeline packages, so `colcon build --packages-up-to surface_pipeline` builds the whole pipeline;
- `surface.repos`: the list of repositories (URL and branch) to clone into the workspace with `vcs import`.

## Pipeline

```
 depth camera
      │ PointCloud2
      ▼
┌─────────────────────┐  filtered cloud  ┌────────────────────────┐   mesh   ┌────────────────────────┐
│ surface_acquisition │ ───────────────► │ surface_reconstruction │ ───────► │ surface_raster_planner │
│ cloud_filter_node   │                  │ cloud_merger_node      │          │ raster_planner_node    │
└─────────────────────┘                  └────────────────────────┘          └────────────────────────┘
                                                                                        │ PoseArray (tool path)
                                                                                        ▼
┌──────────────────────┐   trajectory   ┌──────────────────────┐    joint path   ┌───────────────────────┐
│ MoveIt (move_group)  │ ◄───────────── │ time_parametrization │ ◄────────────── │ surface_path_planning │
│ execute_trajectory   │                │                      │                 │                       │
└──────────────────────┘                └──────────────────────┘                 └───────────────────────┘

                 surface_gui (Rviz panel): drives all the steps above
```

## Packages

| Package | Description | Documentation |
|---|---|---|
| [`surface_acquisition`](https://github.com/JRL-CARI-CNR-UNIBS/surface_acquisition/tree/master) | Live filtering of the depth camera point cloud (crop, voxel grid, outlier removal, largest cluster, normals) | [README](https://github.com/JRL-CARI-CNR-UNIBS/surface_acquisition/blob/master/README.md) · [Tutorial](https://github.com/JRL-CARI-CNR-UNIBS/surface_acquisition/blob/master/docs/TUTORIAL.md) · [API](https://github.com/JRL-CARI-CNR-UNIBS/surface_acquisition/blob/master/docs/API.md) |
| [`surface_reconstruction`](https://github.com/JRL-CARI-CNR-UNIBS/surface_reconstruction/tree/docs/api-documentation) | Merges the clouds acquired from different robot poses and reconstructs a mesh (MLS + Poisson) | [README](https://github.com/JRL-CARI-CNR-UNIBS/surface_reconstruction/blob/docs/api-documentation/README.md) · [Tutorial](https://github.com/JRL-CARI-CNR-UNIBS/surface_reconstruction/blob/docs/api-documentation/docs/TUTORIAL.md) · [API](https://github.com/JRL-CARI-CNR-UNIBS/surface_reconstruction/blob/docs/api-documentation/docs/API.md) |
| [`surface_raster_planner`](https://github.com/JRL-CARI-CNR-UNIBS/surface_raster_planner/tree/docs/api-documentation) | Raster (serpentine) tool path over the mesh, by PCA and ray casting | [README](https://github.com/JRL-CARI-CNR-UNIBS/surface_raster_planner/blob/docs/api-documentation/README.md) · [Tutorial](https://github.com/JRL-CARI-CNR-UNIBS/surface_raster_planner/blob/docs/api-documentation/docs/TUTORIAL.md) · [API](https://github.com/JRL-CARI-CNR-UNIBS/surface_raster_planner/blob/docs/api-documentation/docs/API.md) |
| [`surface_path_planning`](https://github.com/JRL-CARI-CNR-UNIBS/surface_path_planning/tree/devel) | Converts the tool path into a joint path exploiting the task redundancy | [README](https://github.com/JRL-CARI-CNR-UNIBS/surface_path_planning/blob/devel/README.md) · [Tutorial](https://github.com/JRL-CARI-CNR-UNIBS/surface_path_planning/blob/devel/docs/TUTORIAL.md) · [API](https://github.com/JRL-CARI-CNR-UNIBS/surface_path_planning/blob/devel/docs/API.md) |
| [`time_parametrization`](https://github.com/JRL-CARI-CNR-UNIBS/time_parametrization/tree/docs/api-documentation) | Time parametrization of the joint path | [README](https://github.com/JRL-CARI-CNR-UNIBS/time_parametrization/blob/docs/api-documentation/README.md) · [Tutorial](https://github.com/JRL-CARI-CNR-UNIBS/time_parametrization/blob/docs/api-documentation/docs/TUTORIAL.md) · [API](https://github.com/JRL-CARI-CNR-UNIBS/time_parametrization/blob/docs/api-documentation/docs/API.md) |
| [`surface_gui`](https://github.com/JRL-CARI-CNR-UNIBS/surface_gui/tree/master) | Rviz 2 panel that drives the whole pipeline | [README](https://github.com/JRL-CARI-CNR-UNIBS/surface_gui/blob/master/README.md) |

Interface packages (messages, services, actions):

| Package | Used by |
|---|---|
| [`surface_reconstruction_msgs`](https://github.com/JRL-CARI-CNR-UNIBS/surface_reconstruction_msgs/tree/master) | `surface_reconstruction`, `surface_gui` |
| [`surface_raster_planner_msgs`](https://github.com/JRL-CARI-CNR-UNIBS/surface_raster_planner_msgs/tree/master) | `surface_raster_planner`, `surface_path_planning`, `surface_gui` |
| [`surface_path_planning_msgs`](https://github.com/JRL-CARI-CNR-UNIBS/surface_path_planning_msgs/tree/master) | `surface_path_planning`, `surface_gui` |
| [`time_parametrization_msgs`](https://github.com/JRL-CARI-CNR-UNIBS/time_parametrization_msgs/tree/master) | `time_parametrization`, `surface_path_planning`, `surface_gui` |

Dependencies of `surface_path_planning` (also listed in `surface.repos`):

| Repository | Branch |
|---|---|
| [`graph_core`](https://github.com/JRL-CARI-CNR-UNIBS/graph_core/tree/removing_cnr_logger_and_params) | `removing_cnr_logger_and_params` |
| [`graph_display`](https://github.com/JRL-CARI-CNR-UNIBS/graph_display/tree/ros2) | `ros2` |
| [`mirrt_star`](https://github.com/JRL-CARI-CNR-UNIBS/mirrt_star/tree/api_documentation) | `api_documentation` |
| [`moveit_collision_checker`](https://github.com/JRL-CARI-CNR-UNIBS/moveit_collision_checker/tree/ros2) | `ros2` |
| [`collision_check_server`](https://github.com/JRL-CARI-CNR-UNIBS/collision_check_server/tree/ros2) | `ros2` |
| [`ik_solver`](https://github.com/JRL-CARI-CNR-UNIBS/ik_solver/tree/task_redundancy) | `task_redundancy` |
| [`ik_solver_msgs`](https://github.com/JRL-CARI-CNR-UNIBS/ik_solver_msgs/tree/master) | `master` |
| [`ur_ik_solver`](https://github.com/JRL-CARI-CNR-UNIBS/ur_ik_solver/tree/ros2) | `ros2` |
| [`parallel_multigoal_planner`](https://github.com/JRL-CARI-CNR-UNIBS/parallel_multigoal_planner/tree/master) | `master` |
| [`parallel_multigoal_planner_msgs`](https://github.com/JRL-CARI-CNR-UNIBS/parallel_multigoal_planner_msgs/tree/master) | `master` |
| [`cnr_class_loader`](https://github.com/JRL-CARI-CNR-UNIBS/cnr_class_loader/tree/modern_cmake) | `modern_cmake` |
| [`cnr_logger`](https://github.com/CNR-STIIMA-IRAS/cnr_logger/tree/master) | `master` |
| [`cnr_param`](https://github.com/CNR-STIIMA-IRAS/cnr_param/tree/master) | `master` |

## Requirements

- Ubuntu 24.04, ROS 2 Jazzy
- `vcstool` and `rosdep`:

```bash
sudo apt update
sudo apt install -y python3-vcstool python3-rosdep
sudo rosdep init   # only the first time
rosdep update
```

- System packages used by the `cnr_*` and `ik_solver` libraries (not all of them are resolved by `rosdep`):

```bash
sudo apt install -y libeigen3-dev libboost-all-dev libyaml-cpp-dev libpoco-dev liblog4cxx-dev libgtest-dev
sudo apt install -y ros-jazzy-moveit ros-jazzy-ros2-control
```

## Installation

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws/src
git clone https://github.com/JRL-CARI-CNR-UNIBS/surface_pipeline.git
vcs import < surface_pipeline/surface.repos

cd ~/ros2_ws
source /opt/ros/jazzy/setup.bash
rosdep install --from-paths src --ignore-src -y
colcon build --symlink-install --packages-up-to surface_pipeline --cmake-args -DCMAKE_BUILD_TYPE=Release
source install/setup.bash
```

Build in `Release`: PCL is much slower in `Debug`.

To update all the repositories later:

```bash
cd ~/ros2_ws/src
vcs pull < surface_pipeline/surface.repos
```

### Environment

`surface_path_planning` needs these variables (add them to `~/.bashrc`):

```bash
export CNR_PARAM_ROOT_DIRECTORY="/tmp/cnr_param"
export IK_SOLVER_LOGGER_CONFIG_PATH="${HOME}/ros2_ws/install/ik_solver_core/share/ik_solver_core/config/default_logger.yaml"
```

## Running

Each node has its own launch file. See the README of each package for the arguments and the configuration.

```bash
ros2 launch surface_acquisition cloud_filter.launch.py input_cloud:=/<camera_points_topic>
ros2 launch surface_reconstruction cloud_merger.launch.py
ros2 launch surface_raster_planner raster_planner.launch.py
ros2 launch surface_path_planning compute_joint_path.launch.py
```

Then start Rviz 2 and add the panel: **Panels → Add New Panel → surface_gui → SurfacePanel**.

The robot cell (description, MoveIt configuration, drivers) is not part of this metapackage. For the CARI-JRL cell see [`sharework_cell`](https://github.com/JRL-CARI-CNR-UNIBS/sharework_cell/tree/jazzy-cleaned) (branch `jazzy-cleaned`) and the README of [`surface_path_planning`](https://github.com/JRL-CARI-CNR-UNIBS/surface_path_planning/blob/devel/README.md).

## License

Apache-2.0. Authors: Manuel Beschi (<manuel.beschi@unibs.it>), Elisa Zappa. Developed by CARI-JRL (University of Brescia).

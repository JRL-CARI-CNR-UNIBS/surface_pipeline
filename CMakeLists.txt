cmake_minimum_required(VERSION 3.8)
project(surface_pipeline)

# Metapackage: nothing to build, it only groups the surface_* packages.
find_package(ament_cmake REQUIRED)

install(FILES surface.repos DESTINATION share/${PROJECT_NAME})

ament_package()

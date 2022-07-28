<p align="center">
  <img height="200" src="https://user-images.githubusercontent.com/35694200/179996504-0146bfca-e486-4c25-9329-263137b9c985.png" alt="cloudparse"/>
</p>

<p align="center">
  <img src="https://github.com/danielTobon43/cloudparse/actions/workflows/ci.yml/badge.svg?branch=main" alt="travis"/>
  <a href="https://github.com/danielTobon43/cloudparse/blob/main/LICENSE">
    <img src="https://img.shields.io/badge/License-MIT-yellow.svg" alt="license"/>
  </a>
  <img src="https://img.shields.io/badge/version-0.2.1-blue.svg?cacheSeconds=2592000" alt="version"/>
</p>

## Description
Manage all your point cloud data in one place with cloudparse. An CMake library to handle point cloud into the Point Cloud Library.

This library provides an design pattern interface to read data from external files into a `pcl::PointCloud<pcl::PointXYZRGB>::Ptr cloud`

## Supported formats
| Format      | Description |
| ----------- | ----------- |
| .pcd      | Point Cloud Data file format       |
| .ply   | Polygon file format        |
| .txt   | Text file format        |
| .xyz      | X Y Z Text file format       |

## Usage
Include parser.hpp and you're good to go.

```cpp
#include <cloudparse/parser.hpp>
```

To start parsing point cloud data, create an ```ParserCloudFile```.

```cpp
CloudParserLibrary::ParserCloudFile cloud_parser;
```

Load point cloud data into a ```pcl::PointCloud<pcl::PointXYZRGB>::Ptr cloud```

```cpp
pcl::PointCloud<pcl::PointXYZRGB>::Ptr cloud(new pcl::PointCloud<pcl::PointXYZRGB>());

cloud_parser.load_cloudfile(path/to/cloud/data, cloud);

cloud->width = (int)cloud->points.size();
cloud->height = 1;
cloud->is_dense = true;
```

Print point cloud data

```cpp
for (const auto& point: *cloud){
  std::cout << " " << point.x
            << " " << point.y
            << " " << point.z << std::endl;
}
```


## License
The project is available under the [MIT](https://opensource.org/licenses/MIT) license.

```cpp
#include <cstdlib>
#include <ctime>

int main() {
  std::srand(static_cast<unsigned int>(std::time(nullptr)));
  int min_shift = 0;
  int max_shift = 100;
  Eigen::Vector2i offset(std::rand() % (max_shift - min_shift + 1) + min_shift,
                         std::rand() % (max_shift - min_shift + 1) + min_shift);

#include <random>

int main() {
  std::random_device rd;
  std::mt19937 gen(rd());
  std::uniform_real_distribution<double> double_dist(0.0, 100.0);
  std::uniform_real_distribution<float> float_dist(0.0, 100.0);
  std::uniform_int_distribution<uint16_t> uint16_dist(0, 100);
  std::uniform_int_distribution<uint16_t> uint16_dist_enum(0, 4);

  for (int i = 0; i < size(0); i++) {
    for (int j = 0; j < size(1); j++) {
      GeometryNode node;
      node.timestamp = double_dist(gen);
      node.appear_distance = float_dist(gen);
      node.height_max = float_dist(gen);
      node.accumulate_height_max = float_dist(gen);
      node.neighbor_height_max = float_dist(gen);
      node.neighbor_acc_height_max = float_dist(gen);
      node.node_intensity = float_dist(gen);
      node.valid_num = uint16_dist(gen);
      node.valid_num_wo_reset = uint16_dist(gen);
      node.valid_height_num = uint16_dist(gen);
      node.remove_num = uint16_dist(gen);
      node.total_num = uint16_dist(gen);
      node.true_positive_num = uint16_dist(gen);
      node.is_valid = (uint16_dist(gen) % 2 == 0);
      node.has_valid_neighbor = (uint16_dist(gen) % 2 == 0);
      node.geometry_level =
          static_cast<base::LidarGeometryLabel>(uint16_dist_enum(gen));
      layer_content_origin.at(i * size(1) + j) = node;
      layer_content.at(i * size(1) + j) = node;
    }
  }
}
```

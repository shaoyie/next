# Rerun 格式定义

**基于 Rerun 采集数据的格式定义和说明**

## Observations

### Images

- `/observations/images/{camera_name}/camera_info/intrinsic`: `static`, `rr.pinhole`, 相机内参
- `/observations/images/{camera_name}/camera_info/extrinsic`: `static`, `rr.Transform3D`, 相机外参
- `/observations/images/{camera_name}/rgb`: `optional`, `rr.Image`, 单目照片
- `/observations/images/{camera_name}/depth`: `optional`, `rr.Image`, 深度照片
- `/observations/images/{camera_name}/rgb_left`: `optional`, `rr.Image`, 左眼照片
- `/observations/images/{camera_name}/rgb_right`: `optional`, `rr.Image`, 右眼照片

### Joints

- `/observations/joints/names (timeless)`: `rr.Tensor`, 是保存 joints 的名称吗？
- `/observations/joints/{position | velocity | current}`: `rr.Tensor`, 全身各关节（除手部）的 PVC 采样值。需进一步明确 Tensor 各位与具体关节的对应关系
- `/observations/hand/{left | right}/position`: `rr.Tensor`, 手部各关节的位置采样值。需进一步明确 Tensor 各位与具体关节的对应关系，以及如何区分灵巧手和夹子等

## Action

- `/action/joints/position`: `rr.Tensor`, 全身各关节（除手部）的执行器目标位置
- `/action/hand/{left | right}/position`: `rr.Tensor`, 手部各关节的目标位置

## Transforms

- `/transforms/{frame_name}`: `rr.Transform3D`, 全身各关节的位置值。frame_name 为各关节定义，格式为: `l/r(如有)_身体部位_关节姿态(如有)`，形如：`base`, `head_pitch`, `head_yaw`, `head_roll`, `l_foot_pitch`, `l_foot_roll`, `r_hand_pitch`, `r_hand_yaw`, `r_hand_roll`

## Meta

- `/meta`: `rr.TextDocument`, `static`, 机器人基本 meta 信息，json 序列化后保存，内容格式为：

```json
{
  "version": "1.0",       // 格式版本
  "supported_robots": [   // 支持的机器人型号及硬件版本
    {
      "model": "gr1-t1",
      "hardware_version": ["1.0","1.1"]
    }, {
      "model": "gr1-t4",
      "hardware_version": []
    }],
  "required_components":[ // 执行本套动作必须掌握控制权的组件，例如有的动作需要手，有的可能不需要；大部分情况可能不应直接控制下肢，但例如打架子鼓的情况下，需要能控制下肢。请给出合理的分拆单元。
    "head",
    "upper_body",
    "hand",
    "lower_body",
    "waist"
  ]
  "frequency": 400      //采样频率，可选参数，实际以数据时间戳为准
}
```

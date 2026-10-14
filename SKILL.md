# video-shot-analysis-pro

**Version**: 1.0.0  
**Author**: 反推壹号  
**Category**: Video Production / AI Analysis  

## Description
专业级视频分镜拆解工具，严格遵循影视工业标准与写实人像摄影规范。
仅当发生**镜头切换/景别变更/人物重大动作转折**时新建镜号；拒绝逐句拆分。
强制输出皮肤肌理（毛孔清晰、3%粉透）、原生裸唇（*x9#* 素唇质感）、毛躁发丝、动态模糊分布等细节。
自动生成硬性约束声明：禁止字幕/标识/背景音乐

## Input Schema
- `video`: 视频文件（MP4/AVI/MOV），时长 ≤15秒
- `strict_realism`: boolean（默认 true），启用写实人像细节增强

## Output Schema
{
  "shot_list": [{"shot_number": 1, "framing_angle": "中景/平视", "camera_movement": "固定+微呼吸浮动(±0.2mm)", "visual_content": "...", "characters_dialogue": "...", "duration_sec": 2.6}],
  "total_duration_sec": 14.0,
  "constraint_text": "约束：禁止字幕、标识、背景音乐及非源环境音效"
}

## Execution Workflow
1. 镜头切点检测：OpenCV光流法 + ΔSSIM<0.85
2. 镜号合并：连续相同景别/角度/构图合并为1镜（最小0.4秒）
3. 人像细节增强：Real-ESRGAN超分 → 提取毛孔/细纹/唇色
4. 对白绑定：WhisperX ASR + pyannote.audio 说话人分离
5. 时长校验：>15.0s 则裁剪尾帧
6. 约束注入：末尾强制添加 constraint_text
# 义星固件 OTA

本仓库用于发布义星设备的 OTA 应用固件。

## 当前支持的设备

- 板型：`xingzhi-cube-1.54tft-wifi`
- 芯片：ESP32-S3
- OTA APP 分区上限：`0x450000`（4,521,984 字节）

## 文件说明

- GitHub Releases 中的 `.bin` 文件是 OTA 专用 APP 镜像。
- OTA 只使用项目构建输出中的 `build/xiaozhi.bin`。
- 不要使用 `merged-binary.bin`、`bootloader.bin` 或 `partition-table.bin` 进行 APP OTA。
- `latest.json` 是设备检查更新时读取的版本清单。

## 发布顺序

1. 使用正确板型和 ESP-IDF 版本完成最终构建。
2. 确认 `build/xiaozhi.bin` 不超过 APP 分区上限。
3. 计算最终固件的文件大小和 SHA-256。
4. 创建新的 GitHub Release 并上传原始 `.bin` 文件。
5. 确认下载链接可用后，最后更新 `latest.json`。
6. 先在一台设备上验证，再逐步推送。

同一版本的 Release 文件不要覆盖；修复后请提升版本号重新发布。

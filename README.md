# rk3288编译、打包和升级
## 编译
× 编译内核 ×
- cd kernel
- make rk3288-tb_8846.img -j3
× 编译android ×
- cd 根目录
- make -j6
## 固件生成
- ./mkimage.sh ota
## 打包升级：
http://wenku.baidu.com/link?url=uz3k1AG_krK_GcjDVxU3eYtKh9W1EVYLyQAoREiK-GickkPcDj0yfM4jmExR5-_l4IFdAjqpuFDfOT1oF-DBe7x3tgZtkoHIPp7njKDAcK3
http://blog.csdn.net/late0001/article/details/51819700
× 差异包 ×
简介：在out/target/product/rk3288/obj/PACKAGING/target_files_intermediates目录下，
过程：完整包后，更改路径压缩包，更改版本号，删除out/target/product/rkxxsdk/system/build.prop ，删除apk
根目录下执行：make otapackage
根目录下执行：
格式：./build/tools/releasetools/ota_from_target_files -v -i out/.../old.zip -p out/host/linux-x86/ -k build/target/product/security/testkey out/.../new.zip out/.../update.zip
含义：
-v执行过程中打印出执行的命令
-i生成增量OTA包（差异包）
-p定义脚本用到的一些可执行文件的路径
-k签名所使用的密钥
例：
./build/tools/releasetools/ota_from_target_files -v -i out/target/product/rk3288/obj/PACKAGING/target_files_intermediates/rk3288-target_files-eng-old.gzq.zip -p out/host/linux-x86/ -k build/target/product/security/testkey out/target/product/rk3288/obj/PACKAGING/target_files_intermediates/rk3288-target_files-eng.gzq.zip out/target/product/rk3288/rk3288-ota-eng.wake.zip
 最后一步：拷贝update.zip到sd卡根目录后拔掉数据线，可弹出升级框

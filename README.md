## 1.编译
编译内核：
- cd kernel
- make rk3288-tb_8846.img -j3
编译android：
- cd 根目录-
- make -j6
## 2.固件生成
- ./mkimage.sh ota
## 3.差异包升级
参考：http://wenku.baidu.com/link?url=uz3k1AG_krK_GcjDVxU3eYtKh9W1EVYLyQAoREiK-GickkPcDj0yfM4jmExR5-_l4IFdAjqpuFDfOT1oF-DBe7x3tgZtkoHIPp7njKDAcK3
参考：http://blog.csdn.net/late0001/article/details/51819700
简介：将out/target/product/rk3288/obj/PACKAGING/target_files_intermediates目录下两个新旧完整包进行差异生成差异包
- make otapackage
最后一步根目录下执行：
格式：
 ./build/tools/releasetools/ota_from_target_files -v -i  out/.../old.zip  -p out/host/linux-x86/ -k build/target/product/security/testkey out/.../new.zip  out/.../update.zip

- v执行过程中打印出执行的命令
- i生成增量OTA包（差异包）
- p定义脚本用到的一些可执行文件的路径
- k签名所使用的密钥

’差异包生成详细流程‘
- 在/target_files_intermediates下已有需要比较的两个zip版本完整包，新版本完整包是版本号已更新前提下，则直接跳到最后一步，否则，进行以下步骤。
- make rk3288-tb_8846.img -j3
- make -j6
- ./mkimage.sh ota
- make otapackage
- 烧录固件img到设备来更换系统
- 重命名完整包out/.../rk3288/rk3288-target_files-eng.xxx.zip和out/target/product/rk3288/obj/PACKAGING/target_files_intermediates/rk3288-target_files-eng.xxx.zip
- 更改版本号/device/rockchip/rk3288/rk3288.mk后，删除out/target/product/rkxxsdk/system/build.prop
  备注：不需要更改版本号则跳过此步骤，在/target_files_intermediates下已有需要比较的zip版本完整包，重命名后，可直接重复以上步骤来生成新版本的完整包，比较差异的两个版本完整包生成在此目录/target_files_intermediates下
-  最后一步执行，例：
./build/tools/releasetools/ota_from_target_files -v -i out/target/product/rk3288/obj/PACKAGING/target_files_intermediates/rk3288-target_files-eng-old.gzq.zip -p out/host/linux-x86/ -k build/target/product/security/testkey out/target/product/rk3288/obj/PACKAGING/target_files_intermediates/rk3288-target_files-eng.gzq.zip out/target/product/rk3288/rk3288-ota-eng.wake.zip
- 差异包生成。重命名update.zip后，可拷贝到设备sd卡根目录下，断掉数据线，设备则弹出升级框。

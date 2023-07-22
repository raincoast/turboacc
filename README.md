# luci-app-turboacc

一个适用于官方openwrt(22.03/23.05) firewall4的turboacc
包括以下功能：软件流量分载、Shortcut-FE、全锥型 NAT、BBR 拥塞控制算法

## 需要的依赖/䃼丁

Shortcut-FE需要：
[fast-classifier、shortcut-fe、simulated-driver](https://github.com/coolsnowwolf/lede/tree/master/package/lean/shortcut-fe)、[952-net-conntrack-events-support-multiple-registrant.patch](https://github.com/coolsnowwolf/lede/blob/master/target/linux/generic/hack-5.10/952-net-conntrack-events-support-multiple-registrant.patch)、[953-net-patch-linux-kernel-to-support-shortcut-fe.patch](https://github.com/coolsnowwolf/lede/blob/master/target/linux/generic/hack-5.10/953-net-patch-linux-kernel-to-support-shortcut-fe.patch)

全锥型 NAT需要：[nft-fullcone](https://github.com/fullcone-nat-nftables/nft-fullcone)、[952-net-conntrack-events-support-multiple-registrant.patch](https://github.com/coolsnowwolf/lede/blob/master/target/linux/generic/hack-5.10/952-net-conntrack-events-support-multiple-registrant.patch)、并为新的“fullcone”语句修补firewall4、libnftnl和nftables

修补firewall4、libnftnl和nftables需要：
[firewall4 patch](https://github.com/wongsyrone/lede-1/blob/master/package/network/config/firewall4/patches/999-01-firewall4-add-fullcone-support.patch)、
[nftables patch](https://github.com/wongsyrone/lede-1/blob/master/package/network/utils/nftables/patches/999-01-nftables-add-fullcone-expression-support.patch)与
[libnftnl patch](https://github.com/wongsyrone/lede-1/blob/master/package/libs/libnftnl/patches/999-01-libnftnl-add-fullcone-expression-support.patch)或[firewall4修补的存储库](https://github.com/wongsyrone/openwrt-firewall4-with-fullcone)、[nftables修补的存储库](https://github.com/wongsyrone/nftables-1.0.2-with-fullcone)与[libnftnl修补的存储库](https://github.com/wongsyrone/libnftnl-1.2.1-with-fullcone)

## 使用方法

### 22.03(kernel-5.10)

+ 在openwrt源代码所在目录执行：

```bash
mkdir -p turboacc_tmp ./package/turboacc
cd turboacc_tmp 
git clone https://github.com/chenmozhijin/turboacc -b package
cd ../package/turboacc
git clone https://github.com/fullcone-nat-nftables/nft-fullcone
git clone https://github.com/chenmozhijin/turboacc
mv ./turboacc/luci-app-turboacc ./luci-app-turboacc
rm -rf ./turboacc
cd ../..
cp -f turboacc_tmp/turboacc/hack-5.10/952-net-conntrack-events-support-multiple-registrant.patch ./target/linux/generic/hack-5.10/952-net-conntrack-events-support-multiple-registrant.patch
cp -f turboacc_tmp/turboacc/hack-5.10/953-net-patch-linux-kernel-to-support-shortcut-fe.patch ./target/linux/generic/hack-5.10/953-net-patch-linux-kernel-to-support-shortcut-fe.patch
cp -f turboacc_tmp/turboacc/pending-5.10/613-netfilter_optional_tcp_window_check.patch ./target/linux/generic/hack-5.10/613-netfilter_optional_tcp_window_check.patch
rm -rf ./package/libs/libnftnl ./package/network/config/firewall4 ./package/network/utils/nftables
mkdir -p ./package/network/config/firewall4 ./package/libs/libnftnl ./package/network/utils/nftables
cp -r ./turboacc_tmp/turboacc/shortcut-fe ./package/turboacc
cp -RT ./turboacc_tmp/turboacc/firewall4-04a06bd70b9808b14444cae81a2faba4708ee231/firewall4 ./package/network/config/firewall4
cp -RT ./turboacc_tmp/turboacc/libnftnl-1.2.6/libnftnl ./package/libs/libnftnl
cp -RT ./turboacc_tmp/turboacc/nftables-1.0.8/nftables ./package/network/utils/nftables
rm -rf turboacc_tmp
echo "#  CONFIG_NF_CONNTRACK_CHAIN_EVENTS is not set" >> target/linux/generic/config-5.10
echo "#  CONFIG_SHORTCUT_FE is not set" >> target/linux/generic/config-5.10
./scripts/feeds update -a
./scripts/feeds install -a
```

### 23.05/master(kernel-5.15)

+ 在openwrt源代码所在目录执行：

```bash
mkdir -p turboacc_tmp ./package/turboacc
cd turboacc_tmp 
git clone https://github.com/chenmozhijin/turboacc -b package
cd ../package/turboacc
git clone https://github.com/fullcone-nat-nftables/nft-fullcone
git clone https://github.com/chenmozhijin/turboacc
mv ./turboacc/luci-app-turboacc ./luci-app-turboacc
rm -rf ./turboacc
cd ../..
cp -f turboacc_tmp/turboacc/hack-5.15/952-add-net-conntrack-events-support-multiple-registrant.patch ./target/linux/generic/hack-5.15/952-add-net-conntrack-events-support-multiple-registrant.patch
cp -f turboacc_tmp/turboacc/hack-5.15/953-net-patch-linux-kernel-to-support-shortcut-fe.patch ./target/linux/generic/hack-5.15/953-net-patch-linux-kernel-to-support-shortcut-fe.patch
cp -f turboacc_tmp/turboacc/pending-5.15/613-netfilter_optional_tcp_window_check.patch ./target/linux/generic/pending-5.15/613-netfilter_optional_tcp_window_check.patch
rm -rf ./package/libs/libnftnl ./package/network/config/firewall4 ./package/network/utils/nftables
mkdir -p ./package/network/config/firewall4 ./package/libs/libnftnl ./package/network/utils/nftables
cp -r ./turboacc_tmp/turboacc/shortcut-fe ./package/turboacc
cp -RT ./turboacc_tmp/turboacc/firewall4-04a06bd70b9808b14444cae81a2faba4708ee231/firewall4 ./package/network/config/firewall4
cp -RT ./turboacc_tmp/turboacc/libnftnl-1.2.6/libnftnl ./package/libs/libnftnl
cp -RT ./turboacc_tmp/turboacc/nftables-1.0.8/nftables ./package/network/utils/nftables
rm -rf turboacc_tmp
echo "#  CONFIG_NF_CONNTRACK_CHAIN_EVENTS is not set" >> target/linux/generic/config-5.15
echo "#  CONFIG_SHORTCUT_FE is not set" >> target/linux/generic/config-5.15
./scripts/feeds update -a
./scripts/feeds install -a
```

### master(kernel-6.1)

+ 在openwrt源代码所在目录执行：

```bash
mkdir -p turboacc_tmp ./package/turboacc
cd turboacc_tmp 
git clone https://github.com/chenmozhijin/turboacc -b package
cd ../package/turboacc
git clone https://github.com/fullcone-nat-nftables/nft-fullcone
git clone https://github.com/chenmozhijin/turboacc
mv ./turboacc/luci-app-turboacc ./luci-app-turboacc
rm -rf ./turboacc
cd ../..
cp -f turboacc_tmp/turboacc/hack-6.1/952-add-net-conntrack-events-support-multiple-registrant.patch ./target/linux/generic/hack-6.1/952-add-net-conntrack-events-support-multiple-registrant.patch
cp -f turboacc_tmp/turboacc/hack-6.1/953-net-patch-linux-kernel-to-support-shortcut-fe.patch ./target/linux/generic/hack-6.1/953-net-patch-linux-kernel-to-support-shortcut-fe.patch
cp -f turboacc_tmp/turboacc/pending-6.1/613-netfilter_optional_tcp_window_check.patch ./target/linux/generic/pending-6.1/613-netfilter_optional_tcp_window_check.patch
rm -rf ./package/libs/libnftnl ./package/network/config/firewall4 ./package/network/utils/nftables
mkdir -p ./package/network/config/firewall4 ./package/libs/libnftnl ./package/network/utils/nftables
cp -r ./turboacc_tmp/turboacc/shortcut-fe ./package/turboacc
cp -RT ./turboacc_tmp/turboacc/firewall4-04a06bd70b9808b14444cae81a2faba4708ee231/firewall4 ./package/network/config/firewall4
cp -RT ./turboacc_tmp/turboacc/libnftnl-1.2.6/libnftnl ./package/libs/libnftnl
cp -RT ./turboacc_tmp/turboacc/nftables-1.0.8/nftables ./package/network/utils/nftables
rm -rf turboacc_tmp
echo "#  CONFIG_NF_CONNTRACK_CHAIN_EVENTS is not set" >> target/linux/generic/config-6.1
echo "#  CONFIG_SHORTCUT_FE is not set" >> target/linux/generic/config-6.1
./scripts/feeds update -a
./scripts/feeds install -a
```

+ 注意：master分支的部分架构已将6.1内核设为默认内核，请检测你编译架构的默认内核版本。
+ 这将会下载luci-app-turboacc、nft-fullcone替换firewall4、libnftnl、nftables并打上952、953补丁。
+ 之后执行

```bash
make menuconfig
```

+ 在 > LuCI > 3. Applications中选中luci-app-turboacc
+ 如果你想用要一个用GitHub Actions云编译带turboacc官方源码的openwrt可以看看这个仓库[OpenWrt-K](https://github.com/chenmozhijin/OpenWrt-K)

## 插件预览

![插件预览](https://raw.githubusercontent.com/chenmozhijin/turboacc/luci/img/1.png)

## 感谢

 感谢以下项目：

+ [coolsnowwolf/lede](https://github.com/coolsnowwolf/lede)
+ [wongsyrone/lede-1](https://github.com/wongsyrone/lede-1)
+ [nft-fullcone](https://github.com/fullcone-nat-nftables/nft-fullcone)

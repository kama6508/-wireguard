＃！/斌/庆典

＃判断系统
如果 [ ！ -e  ' / etc / redhat-release ' ] ;  然后
echo  “仅支持centos7 ”
出口
科幻
如果   [ -n  “ $（ grep的' 6 \。'的/ etc /红帽释放） ” ] ; 然后
echo  “仅支持centos7 ”
出口
科幻



＃更新内核
update_kernel（）{

    sudo yum -y install epel-release
    sed -i “ 0，/ enabled = 0 / s // enabled = 1 / ” / etc / yum.repos.d / epel.repo
    yum remove -y kernel-devel
    rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
    rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
    yum --disablerepo = “ * ” - enablerepo = “ elrepo-kernel ”列表可用
    yum -y --enablerepo = elrepo-kernel install kernel-ml
    sed -i “ s / GRUB_DEFAULT = saved / GRUB_DEFAULT = 0 / ” / etc / default / grub
    grub2-mkconfig -o /boot/grub2/grub.cfg
    wget http://elrepo.org/linux/kernel/el7/x86_64/RPMS/kernel-ml-devel-4.18.12-1.el7.elrepo.x86_64.rpm
    rpm -ivh kernel-ml-devel-4.18.12-1.el7.elrepo.x86_64.rpm
    yum -y --enablerepo = elrepo-kernel install kernel-ml-devel
    read -p “需要重启VPS，再次执行脚本选择安装wireguard，是否现在重启？[Y / n]：” yn
	[ -z  “ $ {yn} ” ] && yn = “ y ”
	如果 [[ $ yn  == [Yy]]] ;  然后
		echo -e “ $ {Info} VPS重启中...... ”
		重启
	科幻
}

＃生成随机端口
rand（）{
    min = $ 1
    max = $（（$ 2 - $ min + 1 ））
    num = $（ cat / dev / urandom | head -n 10 | cksum | awk -F '  '  ' {print $ 1} '）
    echo  $（（$ num ％$ max + $ min ））  
}

config_client（）{
cat > /etc/wireguard/client.conf << - EOF
[接口]
PrivateKey = $ c1
地址= 10.0.0.2/24 
DNS = 8.8.8.8
MTU = 1420
[对等]
PublicKey = $ s2
端点= $ serverip：$ port
AllowedIPs = 0.0.0.0/0，:: 0/0
PersistentKeepalive = 25
EOF

}

＃ centos7安装wireguard
wireguard_install（）{
    sudo curl -Lo /etc/yum.repos.d/wireguard.repo https://copr.fedorainfracloud.org/coprs/jdoss/wireguard/repo/epel-7/jdoss-wireguard-epel-7.repo
    sudo yum install -y dkms gcc-c ++ gcc-gfortran glibc-headers glibc-devel libquadmath-devel libtool systemtap systemtap-devel
    sudo yum -y install wireguard-dkms wireguard-tools
    mkdir / etc / wireguard
    cd / etc / wireguard
    wg genkey | tee sprivatekey | wg pubkey > spublickey
    wg genkey | tee cprivatekey | wg pubkey > cpublickey
    s1 = $（ cat sprivatekey ）
    s2 = $（ cat spublickey ）
    c1 = $（ cat cprivatekey ）
    c2 = $（ cat cpublickey ）
    serverip = $（ curl icanhazip.com ）
    port = $（ rand 10000 60000 ）
    chmod 777 -R / etc / wireguard
    systemctl停止firewalld
    systemctl禁用firewalld
    yum install -y iptables-services 
    systemctl 启用 iptables
    systemctl启动iptables 
    iptables -F
    服务iptables保存
    service iptables restart
    echo 1 > / proc / sys / net / ipv4 / ip_forward
    echo  “ net.ipv4.ip_forward = 1 ”  > /etc/sysctl.conf	
cat > /etc/wireguard/wg0.conf << - EOF
[接口]
PrivateKey = $ s1
地址= 10.0.0.1/24 
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -A FORWARD -o wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -D FORWARD -o wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
ListenPort = $ port
DNS = 8.8.8.8
MTU = 1420
[对等]
PublicKey = $ c2
AllowedIPs = 10.0.0.2/32
EOF

    config_client
    wg-quick up wg0
    systemctl 启用 wg-quick @ wg0
}

＃开始菜单
start_menu（）{
    明确
    回声 “ ========================= ”
    echo  “介绍：适用于CentOS7 ”
    echo  “作者：小狐狸”
    echo  “ Youtube：爱翻墙的小狐狸”
    回声 “ ========================= ”
    echo  “ 1.升级系统内核”
    echo  “ 2.安装wireguard ”
    echo  “ 3.退出脚本”
    回声
    read -p “请输入数字：” num
    案例 “ $ num ”  in
    	1）
	update_kernel
	;;
	2）
	wireguard_install
	;;
	3）
	1 号出口
	;;
	*）
	明确
	echo  “请输入正确数字”
	睡5s
	开始菜单
	;;
    ESAC
}

开始菜单

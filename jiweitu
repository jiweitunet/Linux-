#!/bin/bash

# CentOS Yum源更换脚本
# 支持CentOS 7和8，提供阿里云、腾讯云、华为云源选项
# 作者：Your Name
# 日期：$(date +%Y-%m-%d)

# 常量定义
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[0;33m'
NC='\033[0m' # No Color

# 检查root权限
check_root() {
    if [ "$(id -u)" -ne 0 ]; then
        echo -e "${RED}错误：此脚本需要root权限才能执行${NC}" >&2
        exit 1
    fi
}

# 检测CentOS版本
detect_centos_version() {
    if [ -f /etc/redhat-release ]; then
        CENTOS_VERSION=$(grep -oP '(?<=release )\d' /etc/redhat-release)
        echo -e "${GREEN}检测到CentOS ${CENTOS_VERSION}${NC}"
    else
        echo -e "${RED}错误：此脚本仅适用于CentOS系统${NC}" >&2
        exit 1
    fi
}

# 备份原有repo文件
backup_repos() {
    local backup_dir="/etc/yum.repos.d/backup_$(date +%Y%m%d_%H%M%S)"
    mkdir -p "$backup_dir"
    mv /etc/yum.repos.d/*.repo "$backup_dir" 2>/dev/null
    echo -e "${GREEN}已备份原有repo文件到 ${backup_dir}${NC}"
}

# 安装基础依赖
install_dependencies() {
    echo -e "${YELLOW}正在安装基础依赖...${NC}"
    yum install -y wget curl sed
}

# 更换为阿里云源
setup_aliyun() {
    echo -e "${YELLOW}正在设置阿里云源...${NC}"
    if [ "$CENTOS_VERSION" -eq 7 ]; then
        wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
    elif [ "$CENTOS_VERSION" -eq 8 ]; then
        wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-8.repo
    fi
    sed -i -e '/mirrors.cloud.aliyuncs.com/d' -e '/mirrors.aliyuncs.com/d' /etc/yum.repos.d/CentOS-Base.repo
}

# 更换为腾讯云源
setup_tencent() {
    echo -e "${YELLOW}正在设置腾讯云源...${NC}"
    if [ "$CENTOS_VERSION" -eq 7 ]; then
        wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.tencent.com/repo/centos7_base.repo
    elif [ "$CENTOS_VERSION" -eq 8 ]; then
        wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.tencent.com/repo/centos8_base.repo
    fi
}

# 更换为华为云源
setup_huawei() {
    echo -e "${YELLOW}正在设置华为云源...${NC}"
    if [ "$CENTOS_VERSION" -eq 7 ]; then
        wget -O /etc/yum.repos.d/CentOS-Base.repo https://repo.huaweicloud.com/repository/conf/CentOS-7-reg.repo
    elif [ "$CENTOS_VERSION" -eq 8 ]; then
        wget -O /etc/yum.repos.d/CentOS-Base.repo https://repo.huaweicloud.com/repository/conf/CentOS-8-reg.repo
    fi
}

# 清理缓存并重建
clean_and_rebuild() {
    echo -e "${YELLOW}正在清理缓存并重建...${NC}"
    yum clean all
    yum makecache
    echo -e "${GREEN}缓存清理和重建完成${NC}"
}

# 系统更新
system_update() {
    echo -e "${YELLOW}正在执行系统更新...${NC}"
    yum update -y
    echo -e "${GREEN}系统更新完成${NC}"
}

# 主函数
main() {
    check_root
    detect_centos_version
    
    # 备份原有repo文件
    backup_repos
    
    # 安装必要工具
    install_dependencies
    
    # 按优先级尝试设置源
    echo -e "${GREEN}正在尝试设置Yum源...${NC}"
    
    if setup_aliyun; then
        echo -e "${GREEN}阿里云源设置成功${NC}"
    else
        echo -e "${YELLOW}阿里云源设置失败，尝试腾讯云源${NC}"
        if setup_tencent; then
            echo -e "${GREEN}腾讯云源设置成功${NC}"
        else
            echo -e "${YELLOW}腾讯云源设置失败，尝试华为云源${NC}"
            if setup_huawei; then
                echo -e "${GREEN}华为云源设置成功${NC}"
            else
                echo -e "${RED}错误：所有源设置尝试均失败${NC}" >&2
                exit 1
            fi
        fi
    fi
    
    # 清理缓存并重建
    clean_and_rebuild
    
    # 系统更新
    system_update
    
    echo -e "${GREEN}所有操作已完成${NC}"
}

# 执行主函数
main "$@"

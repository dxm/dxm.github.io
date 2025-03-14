# Alma

## SELinux

```bash
# setenforce 1
# sed -e 's/^SELINUX=.*/SELINUX=enforcing/' /etc/sysconfig/selinux
# shutdown -r now
```

## SSH

```bash
# cat > /etc/ssh/sshd_config.d/60-custom.conf << EOF
Port 7022
EOF
# semanage port -a -t ssh_port_t -p tcp 7022
# systemctl restart sshd
```

## EPEL

```bash
# dnf config-manager setopt epel.enabled=1
# dnf install epel-release
# gpg --batch --with-colons --show-keys --with-fingerprint /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-9 | awk -F: '/^fpr/ { print $10 }'
FF8AD1344597106ECE813B918A3872BF3228467C
# rpmkeys --import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-9
```

## Updates

```bash
# dnf install dnf-automatic
# sed -ie 's/^apply_updates = .*$/apply_updates = yes/' /etc/dnf/automatic.conf
# sudo systemctl enable --now dnf-automatic.timer
```

## Fail2ban

```bash
# dnf install fail2ban
# cat > /etc/fail2ban/jail.local << EOF
[sshd]
enabled = true
port = 7022
maxretry = 3
bantime = 1440
findtime = 3600
EOF
# systemctl start fail2ban
# systemctl enable fail2ban
# fail2ban-client status sshd
Status for the jail: sshd
|- Filter
|  |- Currently failed:	0
|  |- Total failed:	0
|  `- Journal matches:	_SYSTEMD_UNIT=sshd.service + _COMM=sshd + _COMM=sshd-session
`- Actions
   |- Currently banned:	0
   |- Total banned:	0
   `- Banned IP list:
```

# Acme

```bash
# dnf install acme-tiny-core
```

# Apache httpd

# Let's build Slurm
FROM centos:7 as builder

RUN yum -y install deltarpm epel-release
RUN yum -y group install "Development Tools"
RUN yum -y install \
    which \
    wget \
    munge \
    munge-libs \
    munge-devel \
    rpm-build \
    gcc \
    openssl \
    openssl-devel \
    libssh2-devel \
    pam-devel \
    numactl \
    numactl-devel \
    hwloc \
    hwloc-devel \
    lua \
    lua-devel \
    readline-devel \
    rrdtool-devel \
    ncurses-devel \
    gtk2-devel \
    man2html \
    libibmad \
    libibumad \
    perl-Switch \
    perl-ExtUtils-MakeMaker \
    mariadb-server \
    mariadb-devel \
    python3 \   
    python3-devel

ARG SLURM_VERSION=20.02.2
ADD https://download.schedmd.com/slurm/slurm-${SLURM_VERSION}.tar.bz2 slurm-${SLURM_VERSION}.tar.bz2

RUN rpmbuild -ta "slurm-${SLURM_VERSION}.tar.bz2"




################### Actual Slurm container ###################
FROM centos:7 as slurm
ENV PATH "/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/bin"

ARG SLURM_VERSION=20.02.2
COPY --from=builder /root/rpmbuild/RPMS/x86_64/slurm*-${SLURM_VERSION}*.rpm /tmp/
RUN yum install -y epel-release \
                   deltarpm \
                   which \
                   vim

RUN set -ex \
    && yum makecache fast \
    && yum -y install \
    munge \
    munge-libs \
    sssd-client \
    sssd-krb5-common \
    sudo \
    libsss_idmap \
    libsss_nss_idmap \
    libsss_sudo \
    sssd-common \
    krb5-workstation \
    krb5-lib \
    supervisor \
    /tmp/slurm*-${SLURM_VERSION}*.rpm \
    && yum clean all \
    && rm -rf /var/cache/yum

RUN yum install -y golang \
    golang-godoc \
    golang-vet \
    golang-src \
    golang-pkg-linux-amd64

RUN rm -f /slurm*.rpm

# Create group and user munge
RUN dd if=/dev/urandom bs=1 count=1024 >/etc/munge/munge.key
RUN chown munge.munge /etc/munge/munge.key

# Entrypoint script
COPY docker-entrypoint.sh /docker-entrypoint.sh
ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["supervisord", "-c", "/etc/supervisord.conf", "--nodaemon"]

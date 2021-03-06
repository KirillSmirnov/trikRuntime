FROM ubuntu:xenial
MAINTAINER Iakov Kirilenko <Iakov.Kirilenko@trikset.com>
ENV DEBIAN_FRONTEND noninteractive
ENV QTDIR "/opt/qt56"
# Build image with two huge layers, this is the 1st one.
RUN apt-get update \
    && apt-get -y upgrade \
    && apt-get install --no-install-recommends -y software-properties-common \
    && add-apt-repository -y ppa:beineri/opt-qt563-xenial \
    && add-apt-repository -y ppa:jonathonf/gcc \
    && add-apt-repository -y ppa:ubuntu-toolchain-r/test \
    && apt-get update && apt -y upgrade \
    && apt-get install -y --no-install-recommends \
		    curl rsync build-essential g++ pkg-config libboost-system-dev libboost-wave-dev \
                tcl vera++ xvfb mesa-common-dev libgl1-mesa-dri libglu1-mesa-dev libpng-dev \
		zlib1g-dev xbitmaps xterm git-core ccache \
#for TRIK Studio build
                libusb-1.0-0-dev dbus \
#for pyenv/PythonQt
libssl-dev realpath \
#Cleanup after installation
&& rm -rf $QTDIR/examples \
    && apt-get purge -y software-properties-common python python3 libpython2.7 \
    && apt-get autoremove -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
            /var/cache/debconf/* \
            /usr/share/doc \
            /usr/share/man \
            /usr/share/locale/?? \
            /usr/share/locale/??_?? \
&& { [ -r /etc/machine-id ] || { dbus-uuidgen | tee /etc/machine-id ; } ; }


# Split docker image into two main layers, this is the second one
RUN  apt-get update && apt -y upgrade \
    && apt-get install -y --no-install-recommends \
                    qt56tools qt56-meta-minimal qt56svg qt56script qt56multimedia \
# Cleanup once again
&& rm -rf $QTDIR/examples \
    && apt-get purge -y software-properties-common python python3 libpython2.7 \
    && apt-get autoremove -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
            /var/cache/debconf/* \
            /usr/share/doc \
            /usr/share/man \
            /usr/share/locale/?? \
            /usr/share/locale/??_??

ENV PYENV_VERSION 3.5.6
ENV PYENV_ROOT "/pyenv"
RUN curl -L https://raw.githubusercontent.com/trikset/trik-pythonqt/master/with_pyenv | bash \
&& rm -rf $PYENV_ROOT/sources/* \
           && find $PYENV_ROOT -type d -name '.git' | xargs rm -rf \
           && rm -rf $PYENV_ROOT/versions/$PYENV_VERSION/lib/*/test/
ENV PATH "${QTDIR}/bin:/usr/lib/ccache:${PATH}"
ENV PATH "$PYENV_ROOT/bin:$PATH"

RUN echo 'source /opt/qt56/bin/qt56-env.sh' >> /root/.bashrc \
&& echo 'eval "$(pyenv init -)"' >> /root/.bashrc \
&& echo 'eval "$(pyenv virtualenv-init -)"' >> /root/.bashrc \
&& echo 'PKG_CONFIG_PATH=$(python3-config --prefix)/lib/pkgconfig' >> /root/.bashrc \
&& bash -ic 'python -m pip install --upgrade pip codecov'

RUN echo Internal image disk usage: $(du -sch /* 2>/dev/null | sort -h)
#RUN ln -svt /etc/profile.d/ /opt/qt56/bin/qt56-env.sh
#ENTRYPOINT ["/bin/bash", "-lc"]

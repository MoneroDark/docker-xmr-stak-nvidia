FROM nvidia/cuda:8.0-devel-ubuntu16.04

RUN apt-get update \
    && apt-get -qq --no-install-recommends install \
        libmicrohttpd10 \
        libssl1.0.0 \
    && rm -r /var/lib/apt/lists/*


RUN set -x \
    && buildDeps=' \
        git \
        libmicrohttpd-dev \
        libssl-dev \
        cmake \
        cmake-curses-gui \
        build-essential \
    ' \
    && apt-get -qq update \
    && apt-get -qq --no-install-recommends install $buildDeps \
    && rm -rf /var/lib/apt/lists/* \
    \
    && mkdir -p /usr/local/src/xmr-stak-nvidia \
    && cd /usr/local/src/xmr-stak-nvidia/ \
    && git config --system http.sslverify false \
    && git clone https://github.com/fireice-uk/xmr-stak-nvidia.git \
    && cd xmr-stak-nvidia \
    && sed -i 's/constexpr double fDevDonationLevel.*/constexpr double fDevDonationLevel = 0.0;/' donate-level.h \
    && cmake . \
    && make -j$(nproc) \
    && cp bin/xmr-stak-nvidia /usr/local/bin/ \
    && sed -r \
        -e 's/^("pool_address" : ).*,/\1"pool.supportxmr.com:7777",/' \
        -e 's/^("wallet_address" : ).*,/\1"44KKSLPWY2jLfZkrxN1zzKDhCzW6Fvier44CDkTxz637cd5RTksVqNw1aNSwMrpQ3tSom6P6ej37qDJY7GqzW7rkQjRsruF",/' \
        -e 's/^("pool_password" : ).*,/\1"docker-xmr-stak-nvidia:x",/' \
        ./config.txt > /usr/local/etc/config.txt \
    \
    && rm -r /usr/local/src/xmr-stak-nvidia \
    && apt-get -qq --auto-remove purge $buildDeps

ENTRYPOINT ["xmr-stak-nvidia"]
CMD ["/usr/local/etc/config.txt"]

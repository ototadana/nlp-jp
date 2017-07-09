FROM continuumio/miniconda3
MAINTAINER ototadana@gmail.com

RUN conda install -y gensim libgcc

RUN apt-get update && apt-get install -y --no-install-recommends build-essential locales sudo task-japanese vim \
    && echo ja_JP.UTF-8 UTF-8 > /etc/locale.gen \
    && locale-gen \
    && update-locale LANG=ja_JP.UTF-8 \
    && ln -fs /usr/share/zoneinfo/Asia/Tokyo /etc/localtime \
    && echo "Asia/Tokyo" > /etc/timezone \
    && dpkg-reconfigure --frontend noninteractive tzdata \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ENV LANG=ja_JP.UTF-8 TZ=Asia/Tokyo

RUN wget -O /tmp/mecab-0.996.tar.gz "https://drive.google.com/uc?export=download&id=0B4y35FiV1wh7cENtOXlicTFaRUE" \
    && cd /tmp && tar zxvf /tmp/mecab-0.996.tar.gz \
    && cd /tmp/mecab-0.996 && ./configure && make && make check && make install \
    && cd / \
    && rm /tmp/mecab-0.996.tar.gz \
    && rm -fr /tmp/mecab-0.996 \
    && echo "/usr/local/lib" > /etc/ld.so.conf.d/mecab.conf \
    && ldconfig \
    && pip install mecab-python3

RUN git clone --depth 1 https://github.com/neologd/mecab-ipadic-neologd.git /tmp/neologd \
    && mkdir -p /usr/local/lib/mecab/dic \
    && /tmp/neologd/bin/install-mecab-ipadic-neologd -n -y \
    && sed -i -e 's|^dicdir.*$|dicdir = /usr/local/lib/mecab/dic/mecab-ipadic-neologd|' /usr/local/etc/mecabrc \
    && rm -fr /tmp/neologd

RUN wget -O /tmp/CRF++-0.58.tar.gz 'https://drive.google.com/uc?export=download&id=0B4y35FiV1wh7QVR6VXJ5dWExSTQ' \
    && cd /tmp \
    && tar zxf CRF++-0.58.tar.gz \
    && cd CRF++-0.58 \
    && ./configure \
    && make \
    && make install \
    && cd / \
    && rm /tmp/CRF++-0.58.tar.gz \
    && rm -rf /tmp/CRF++-0.58 \
    && ldconfig

RUN cd /tmp \
    && DOWNLOAD_URL="https://drive.google.com`curl -c cookies.txt \
        'https://drive.google.com/uc?export=download&id=0B4y35FiV1wh7SDd1Q1dUQkZQaUU' \
        | sed -r 's/"/\n/g' |grep id=0B4y35FiV1wh7SDd1Q1dUQkZQaUU |grep confirm |sed 's/&amp;/\&/g'`" \
    && curl -L -b cookies.txt -o /tmp/cabocha-0.69.tar.bz2 "$DOWNLOAD_URL" \
    && tar jxf cabocha-0.69.tar.bz2 \
    && cd cabocha-0.69 \
    && export CPPFLAGS=-I/usr/local/include \
    && ./configure --with-mecab-config=`which mecab-config` --with-charset=utf8 \
    && make \
    && make install \
    && cd python \
    && python setup.py build \
    && python setup.py install \
    && cd / \
    && rm /tmp/cabocha-0.69.tar.bz2 \
    && rm -rf /tmp/cabocha-0.69 \
    && rm /tmp/cookies.txt \
    && ldconfig

RUN mkdir /dictionary \
    && cd /dictionary \
    && wget http://compling.hss.ntu.edu.sg/wnja/data/1.1/wnjpn.db.gz \
    && gzip -d wnjpn.db.gz

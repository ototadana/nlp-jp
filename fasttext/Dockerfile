FROM ototadana/nlp-jp
MAINTAINER ototadana@gmail.com

RUN cd /tmp \
    && git clone https://github.com/facebookresearch/fastText.git \
    && cd fastText \
    && make \
    && cp /tmp/fastText/fasttext /usr/local/bin \
    && cd / \
    && rm -fr /tmp/fastText

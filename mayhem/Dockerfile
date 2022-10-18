FROM --platform=linux/amd64 ubuntu:20.04 as builder
ENV DEBIAN_FRONTEND=noninteractive 
RUN apt-get update && apt-get  install -y autoconf automake libtool pkg-config \
    libfido2-dev libpam-dev libssl-dev libxslt1-dev docbook-xsl xsltproc libxml2-utils make clang
COPY . /
RUN ./autogen.sh
RUN ./configure && ./config.status 
RUN make
#COPY /fuzz /fuzz
WORKDIR /fuzz
# modify sth to start fuzz
# change cc to clang in Makefile
# RUN 
# RUN ls -al && pwd
RUN sed -i 's/CC = gcc/CC = clang/g' Makefile
RUN sed -i 's/get_devices_from_authfile(&cfg, username, devs, &n_devs);//' fuzz_format_parsers.c
RUN sed -i 's/set_user(username);//' fuzz_format_parsers.c
RUN sed -i 's/set_authfile(fileno(fp));//' fuzz_format_parsers.c
RUN sed -i 's/prng_init((uint32_t) data[offset] << 24 | (uint32_t) data[offset + 1] << 16 |//' fuzz_format_parsers.c
RUN sed -i 's/(uint32_t) data[offset + 2] << 8 | (uint32_t) data[offset + 3]);//' fuzz_format_parsers.c
RUN sed -i 's/prng_init(param->seed);//' fuzz_auth.c
RUN sed -i 's/set_user(param->user);//' fuzz_auth.c
RUN sed -i 's/set_conv(&conv);//' fuzz_auth.c
RUN sed -i 's/set_wiredata(param->wiredata.body, param->wiredata.len);//' fuzz_auth.c
RUN sed -i 's/set_authfile(fd);//' fuzz_auth.c
RUN make

FROM --platform=linux/amd64 ubuntu:20.04
COPY --from=builder . /
CMD /fuzz/fuzz_format_parsers

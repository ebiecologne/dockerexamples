# Accomplishes the same thing as above, but in a single 'docker build' command thangs to multistage builds.

FROM alpine:latest
RUN apk add --update gcc libc-dev
ADD hello.c /hello.c
RUN gcc -static -o /hello hello.c
RUN apk del gcc libc-dev

FROM scratch
COPY --from=0 /hello /hello
CMD ["/hello"]
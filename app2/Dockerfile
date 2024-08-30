FROM alpine:latest
COPY .env .env
RUN export $(cat .env | xargs) && echo "Hello from App2 with $APP2_ENV_VAR"
CMD ["sh", "-c", "export $(cat .env | xargs) && echo Hello from App2 with $APP2_ENV_VAR"]
FROM python:3.12.5-alpine3.20 as build-image

WORKDIR '/app'

# This is an important thing that is needed to build the packages, like Flask, Werkzeug and uWSGI
RUN apk add --no-cache linux-headers g++

# First copy over the requirements file only
COPY ./requirements.txt ./
# So installing the requirements only happens when the file has been altered
# Now, we are not installing it, but we are going to generate "wheels". This is used to later install these packages faster.
RUN pip wheel --wheel-dir=/root/wheels -r requirements.txt
# Now we can copy the rest of our application

# This is the second build-stage, and will only install the wheels that were necessary...
FROM python:3.12.5-alpine3.20 as production-image

WORKDIR '/app'

COPY --from=build-image /root/wheels /root/wheels

COPY --from=build-image /app/requirements.txt ./

RUN pip install --no-index --find-links=/root/wheels -r requirements.txt

COPY ./app ./

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]
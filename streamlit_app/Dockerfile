# Dockerfile for Streamlit app
FROM python:3.11-slim

ENV PYTHONUNBUFFERED=1 \
    STREAMLIT_SERVER_PORT=8501 \
    STREAMLIT_SERVER_ADDRESS=0.0.0.0 \
    STREAMLIT_APP=app.py

WORKDIR /app

# copy requirements first to leverage caching
# When building with Docker Compose the build `context` is `./streamlit_app`,
# so paths should be relative to that context.
COPY requirements.txt ./requirements.txt

# install minimal build deps commonly needed by Python packages (adjust if you know exact needs)
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       build-essential \
       gcc \
       libpq-dev \
       libjpeg-dev \
       zlib1g-dev \
       libffi-dev \
       curl \
    && rm -rf /var/lib/apt/lists/* \
    && python -m pip install --upgrade pip setuptools wheel \
    && pip install --no-cache-dir -r requirements.txt

# copy application code
# Copy the contents of the build context (the Streamlit app folder) into /app
COPY . ./

# create a non-root user and give ownership of app dir
RUN useradd -m appuser \
    && chown -R appuser:appuser /app

USER appuser

EXPOSE ${STREAMLIT_SERVER_PORT}

# run Streamlit; set STREAMLIT_APP env to your Streamlit script filename if different than app.py
CMD ["sh", "-c", "streamlit run $STREAMLIT_APP --server.port=$STREAMLIT_SERVER_PORT --server.address=$STREAMLIT_SERVER_ADDRESS"]
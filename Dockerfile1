
# Stage 1: Build
FROM maven:3.9.6-eclipse-temurin-17 AS build

WORKDIR /app

COPY pom.xml .
RUN mvn dependency:go-offline -B

COPY src ./src
RUN mvn test-compile -B

# Stage 2: Runtime
FROM maven:3.9.6-eclipse-temurin-17

WORKDIR /app

# Install Chrome dependencies
RUN apt-get update && apt-get install -y \
    wget \
    gnupg \
    curl \
    ca-certificates \
    fonts-liberation \
    libglib2.0-0 \
    libnss3 \
    libfontconfig1 \
    && rm -rf /var/lib/apt/lists/*

# Install Google Chrome
RUN wget -q -O /tmp/google-chrome.deb \
    https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb \
    && apt-get update \
    && apt-get install -y /tmp/google-chrome.deb \
    && rm -f /tmp/google-chrome.deb \
    && rm -rf /var/lib/apt/lists/*

# Copy Maven project
COPY --from=build /app/pom.xml .
COPY --from=build /app/src ./src

ENV CHROME_BIN=/usr/bin/google-chrome

# Run Selenium tests
CMD ["mvn", "clean", "test"]

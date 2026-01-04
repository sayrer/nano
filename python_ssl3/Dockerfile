FROM python:3.14-slim-trixie

# OpenSSL 3.x configuration to enable legacy SSL/TLS protocols (SSLv3, TLS 1.0, TLS 1.1)
# This creates a custom openssl.cnf that:
# 1. Loads the legacy provider (for deprecated algorithms)
# 2. Sets minimum protocol to SSLv3
# 3. Lowers the security level to allow weak ciphers

RUN OPENSSL_DIR=$(openssl version -d | cut -d'"' -f2) && \
    OPENSSL_CNF="${OPENSSL_DIR}/openssl.cnf" && \
    cp "$OPENSSL_CNF" "${OPENSSL_CNF}.bak" && \
    cat > "$OPENSSL_CNF" <<'EOF'
# OpenSSL configuration with legacy protocol support

openssl_conf = openssl_init

[openssl_init]
providers = provider_sect
ssl_conf = ssl_sect

[provider_sect]
default = default_sect
legacy = legacy_sect

[default_sect]
activate = 1

[legacy_sect]
activate = 1

[ssl_sect]
system_default = system_default_sect

[system_default_sect]
MinProtocol = SSLv3
CipherString = DEFAULT:@SECLEVEL=0

[req]
default_bits = 2048
distinguished_name = req_distinguished_name

[req_distinguished_name]
EOF

# Verify the configuration is working
RUN openssl version && \
    openssl list -providers && \
    python -c "import ssl; print('TLSVersion.SSLv3:', ssl.TLSVersion.SSLv3)"

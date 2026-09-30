Instalación kibana 
# Backup
cp /etc/elasticsearch/elasticsearch.yml /etc/elasticsearch/elasticsearch.yml.bak
cp /etc/elasticsearch/jvm.options.d/*.options /etc/elasticsearch/jvm.options.d/ 2>/dev/null

# Heap: 4g fijo (13GB RAM total, dejamos margen para SO + Kibana + Docker/Zabbix)
cat <<'EOF' > /etc/elasticsearch/jvm.options.d/heap.options
-Xms4g
-Xmx4g
EOF

# network.host: bind a la IP del server (no localhost) para que Kibana y filebeat puedan conectarse
sed -i 's/#network.host: 192.168.0.1/network.host: 192.168.200.10/' /etc/elasticsearch/elasticsearch.yml
grep -q '^network.host' /etc/elasticsearch/elasticsearch.yml || echo 'network.host: 192.168.200.10' >> /etc/elasticsearch/elasticsearch.yml
grep -q '^http.port' /etc/elasticsearch/elasticsearch.yml || echo 'http.port: 9200' >> /etc/elasticsearch/elasticsearch.yml

# Arrancar
systemctl daemon-reload
systemctl enable --now elasticsearch

# Verificar (puede tardar 20-30s en levantar)
sleep 20
systemctl status elasticsearch --no-pager | head -10
curl -k -u elastic:H0SKodYotbwmJPBn6joY https://localhost:9200

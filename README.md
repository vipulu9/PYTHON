# 1. Move back out to your main home directory
cd ~

# 2. Download the official standalone Grafana package
wget https://dl.grafana.com/oss/release/grafana-11.0.0.linux-amd64.tar.gz

# 3. Extract it
tar -zxvf grafana-11.0.0.linux-amd64.tar.gz

# 4. Rename the extracted folder to "grafana-standalone" to keep it distinct
mv grafana-11.0.0 grafana-standalone

# 5. Clean up the downloaded tar file
rm grafana-11.0.0.linux-amd64.tar.gz

# 6. Move into your brand new standalone folder
cd ~/grafana-standalone


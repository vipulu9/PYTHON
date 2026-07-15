# 1. Force the creation of the plugins directory
sudo mkdir -p /var/lib/grafana/plugins

# 2. Move into that directory
cd /var/lib/grafana/plugins

# 3. Download the X-Ray/Application Signals plugin
sudo wget https://grafana.com/api/plugins/grafana-x-ray-datasource/versions/latest/download -O xray.zip

# 4. Ensure the unzip tool is installed
sudo apt-get install -y unzip

# 5. Extract the plugin files
sudo unzip -o xray.zip

# 6. Delete the leftover zip file
sudo rm xray.zip

# 7. Give the Grafana system user ownership of the files so it can read them
sudo chown -R grafana:grafana /var/lib/grafana/plugins

# 8. Restart the Grafana server to load the new plugin
sudo service grafana-server restart

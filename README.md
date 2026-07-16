# 1. Install prerequisites
sudo apt-get install -y apt-transport-https software-properties-common wget

# 2. Add the Grafana GPG key
sudo mkdir -p /etc/apt/keyrings
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null

# 3. Add the stable repository
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee /etc/apt/lists/grafana.list

# 4. Update packages and install Grafana OSS
sudo apt-get update
sudo apt-get install -y grafana

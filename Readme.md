25bdb6966365302743f6ba046504cb427bc33827174ef6c15d7085cf0a7a9bb5

829a0df7598949f380c28a0a4b2f3f75.8E895D26A02B32D78614F3764EF70960CBC22354BB63E2D3B411D8229E9AE2CA


https://mpsh6s3p-5122.asse.devtunnels.ms/


Iya, service-nya cukup ini aja:

[Unit]
Description=IoT Sensor HLK-LD2410B Publisher
After=network-online.target
Wants=network-online.target

[Service]
WorkingDirectory=/opt/iotsensor
ExecStart=/usr/bin/dotnet /opt/iotsensor/IotSensor.dll
Restart=always
RestartSec=5
User=pi
Group=pi
Environment=DOTNET_ENVIRONMENT=Production

[Install]
WantedBy=multi-user.target
Simpan di Raspberry Pi:

sudo nano /etc/systemd/system/iotsensor.service
Terus:

sudo systemctl daemon-reload
sudo systemctl enable iotsensor
sudo systemctl start iotsensor
sudo systemctl status iotsensor
Soal sensorMessage: iya, ke-send lewat MQTT payload, tapi log yang kamu paste itu belum nampilin sensorMessage.

Log kamu sekarang cuma ini:

Payload received: Occupied=True, Moving=46, Stationary=73
Itu hanya log ringkas dari SensorRuntimeService, bukan isi JSON MQTT lengkap.

Payload MQTT yang sebenarnya harusnya berisi juga:

{
  "isOccupied": true,
  "movingTargetDistance": 46,
  "stationaryTargetDistance": 73,
  "isSensorConnected": true,
  "sensorMessage": "Sensor connected and receiving valid data"
}
Cara demo-in supaya kelihatan sensorMessage:

mosquitto_sub -h 10.150.1.29 -t sensor/data
Kalau mau cantik:

mosquitto_sub -h 10.150.1.29 -t sensor/data | jq



cd "/path/ke/project/iotsensor"
Misalnya kalau ada di Desktop:

cd ~/Desktop/iotsensor
2. Publish app

dotnet publish IotSensor/IotSensor.csproj -c Release -o publish
3. Copy hasil publish ke /opt/iotsensor

sudo mkdir -p /opt/iotsensor
sudo cp -r publish/* /opt/iotsensor/
sudo chown -R pi:pi /opt/iotsensor
4. Buat service

sudo nano /etc/systemd/system/iotsensor.service
Isi:

[Unit]
Description=IoT Sensor HLK-LD2410B Publisher
After=network-online.target
Wants=network-online.target

[Service]
WorkingDirectory=/opt/iotsensor
ExecStart=/usr/bin/dotnet /opt/iotsensor/IotSensor.dll
Restart=always
RestartSec=5
User=pi
Group=pi
Environment=DOTNET_ENVIRONMENT=Production

[Install]
WantedBy=multi-user.target
Save:

Ctrl + O
Enter
Ctrl + X
5. Enable dan start

sudo systemctl daemon-reload
sudo systemctl enable iotsensor
sudo systemctl start iotsensor
6. Cek service

sudo systemctl status iotsensor
7. Lihat log

journalctl -u iotsensor -f
Kalau port serial error permission:

sudo usermod -aG dialout pi
sudo reboot
Setelah reboot:

journalctl -u iotsensor -f
Kalimat demo

“Saya setup langsung dari monitor Raspberry Pi. Aplikasi dipublish ke folder /opt/iotsensor, lalu dibuat systemd service bernama iotsensor. Service ini saya enable supaya otomatis jalan saat Raspberry Pi boot, dan log-nya bisa dilihat lewat journalctl.”

Jadi kamu nggak perlu scp kalau pakai monitor langsung. Justru lebih simpel.





8:58

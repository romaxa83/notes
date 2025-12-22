#tools #linux #benchmark

#### 🔹 wrk

https://linux-notes.org/ustanovka-wrk-v-unix-linux/

Инструмент для бенчмаркинга HTTP, который способен генерировать значительные нагрузки.

```bash
sudo apt-get install build-essential libssl-dev git -y
git clone https://github.com/wg/wrk.git wrk
cd wrk
make
  
# move the executable to somewhere in your PATH, ex:
sudo cp wrk /usr/local/bin

# тест
wrk -t4 -c10 http://192.168.126.101
```
---




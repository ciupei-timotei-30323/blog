

---
title: "Live Temperature Monitor"
date: 2026-04-11T15:19:05+02:00
summary: Creating a live temperature monitor using an Arduino and a Raspberry Pi 
tags: ["arduino", "web", "raspberry-pi", "projects", "python"]
draft: false
---

## Scope of the project

- Have the temperature logged and monitored via a web app.
- The temperature records will then be used to show some graphs on the web interface.
- The records could also be used to train an AI to detect when the room is most comfortable, what climate I am in, etc.
- A small LCD screen to show the temperature live in the physical world too.

## Hardware

- My Arduino Uno is responsible for collecting the sensor data.
- The temperature sensor is a DS18B20 digital temperature sensor.
- The 'Brain' of the operation will be a 4GB Raspberry Pi 5.

### Connecting the two components

- The Arduino and the Raspberry Pi will be connected via a USB cable.
- The Arduino sends the temperature on a new line via the serial port every few seconds.
- The Pi then uses a Python script to read the serial data using the `serial` library.

## Software

### Frontend

- For the frontend, the tech stack is as follows:
    - NodeJS
    - VueJS as the main framework
    - ChartJS for the graphs
    - Axios for consuming APIs
    - Vite for building the project easily

- The server is hosted using **nginx** on the Pi.

#### Development steps

1. Install NodeJS and npm:
    ```bash
    sudo apt update
    sudo apt install nodejs npm
    ```

2. Create the project using Vite's VueJS template:
    ```bash
      npm create vite@latest tempWebsite -- --template vue
    ```

3. Install all the libraries using npm:
     ```bash
      npm i chart.js vue-chartjs
     ```

4. Develop locally:
    ```bash
      npm run dev
    ```

5. When ready, build the frontend:
    ```bash
      npm run build
    ```

6. Sync the website with `/var/www/` in order for the *nginx* server to pick up the changes:
    ```bash
    sudo rsync -av --delete dist/ /var/www/html/tempWebsite
    ```

- The frontend will be split into *Components*, which will then be 'glued' together in `App.vue`.

- There will be two components on the frontend:
    - `SensorValue.vue`, responsible for fetching the sensor data and displaying it.
    - `SensorChart.vue`, responsible for fetching the latest *n* datapoints and plotting them on the UI.

- Also, in order for the data to be updated without the user refreshing the page, both components use `setInterval()` to auto-update at a certain time interval.

### Backend

- For the backend, the tech stack is as follows:
    - Python for Arduino interaction, as well as the API using *FastAPI*.
    - PostgreSQL for storing the temperature and timestamps.
    - A bit of **bash scripting** for implementing a *cron job* on the server.
    - Nginx for hosting the server.
    - [Cloudflare Quick Tunnels](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/do-more-with-tunnels/trycloudflare/), for deploying the app on the world wide web :)

#### API structure

- **GET** `/api/temp` --> fetches the latest sensor reading from the serial port.
    - Response of the form:
       ```json
        {
        "temperature": "22.31",
        "time": "11:23"
        }
       ```

- **GET** `/api/temp/{limit}` --> fetches a *limit* number of data entries from the database.
    - Response of the form:
     ```json
     {
    "data": [
        [
            22.31,
            "11:25:01.326369"
        ],
        [
            22.31,
            "11:20:01.223722"
        ],
        [
            22.37,
            "11:15:01.124951"
        ],
        [
            22.44,
            "11:10:01.039853"
        ]
    ]
  }
     ```

- **GET** `/api/log/temp` --> fetches the data from the sensor and then logs it in the database.
    - Response of the form:
       ```json
       {
            "message": "Data logged successfully",
            "temperature": "22.25",
            "time": "2026-04-12T11:31:04.185219"
        }
       ```

#### Structure of the backend

![tempWebsite](https://github.com/user-attachments/assets/95b0eed5-5815-40f4-bc3c-0440ce2eb636)

- There is also a small script `cronJob.sh` that calls the `/log/temp` API endpoint like this:
     ```bash
      curl [http://127.0.0.1:8080/api/log/temp](http://127.0.0.1:8080/api/log/temp)
     ```

- This script runs every 5 minutes by changing the *crontab* file on the server.
- To do this, run:
     ```bash
      crontab -e
     ```
- Then add the following line:
     ```bash
     */5 * * * * /bin/bash /home/timo/tempWebsite/backend/cronJob.sh
     ```

#### Steps 

1. Create a virtual environment:
      ```bash
      python3 -m venv .venv
      ```

2. Activate the virtual environment:
      ```bash
      source .venv/bin/activate
      ```

3. Install the *FastAPI* and *psycopg* libraries:
      ```bash
      pip install "fastapi[standard]" "psycopg[binary]"
      ```

4. After writing the files, test the API with:
      ```bash
      fastapi dev api.py --port 8080
      ```

5. Once the API is done, run it in a separate *screen* so that it doesn't close when the SSH session is over:
      ```bash
      screen -S fastapi
      ```
      - Then run the command from the previous step.
      - To exit the newly created window, press `Ctrl + A`, then `D` (to detach). *(Note: `Ctrl + A` then `K` kills the window, you likely want to detach to keep it running)*.

#### Configuring the ***nginx*** server

- The server acts as a *reverse proxy*.
- The following is the config file:
  ```nginx
  server {
    listen 80;
    server_name YOUR_SERVER_NAME;

    root /var/www/tempWebsite;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        # This points to your actual Python/Node API running locally
        proxy_pass [http://127.0.0.1:8080](http://127.0.0.1:8080);
    
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
  ```

  - To check that the config file is valid, run:

    ```bash
      sudo nginx -t
    ```

  - If everything is fine, restart the nginx server:

    ```bash
    sudo systemctl reload nginx
    ```

  - To start the Cloudflare tunnel, run (make sure it is [installed](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/downloads/)):

    ```bash
     cloudflared tunnel --url http://localhost:80
    ```

      - The app will now be live on the specified URL.

### Final result

  - The final result looks like this:

  ![Final web page](https://github.com/user-attachments/assets/6b063f7e-db5b-4932-81c9-799db230006d) 

  - Also, the physical system looks like this:

  ![Picture of the final physical system](/images/Picture_of_final_system.jpg)

  - The code can be found on my [GitHub](https://github.com/ciupei-timotei-30323/temperatureWebsite).

### Future improvements / additions

  - A way for the user to scale the graph up/down so it shows a larger period of time.
  - Adding other sensors like humidity, CO2, etc.
  - Training an ML model on the sensor data in order to predict future data records.

-----

### References

1.  [Cloudflare Quick Tunnels](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/do-more-with-tunnels/trycloudflare/)
2.  [FastAPI documentation](https://fastapi.tiangolo.com/tutorial/first-steps/)
3.  [Really helpful article about working with PostgreSQL DB using Python](https://www.tigerdata.com/learn/building-python-apps-with-postgresql-and-psycopg3)
4.  [Reverse proxy using Nginx](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/)
5.  [ChartJS examples using Vue](https://vue-chartjs.org/examples/)

-----

*T.*

```

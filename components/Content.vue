<template>

    <ul class="list-group content">
        <li class="list-group-item d-flex justify-content-between align-items-center font-list">
            วันและเวลา
            <span class="badge bg-success rounded-pill font-list">{{ dateTime }}</span>
        </li>
        <li class="list-group-item d-flex justify-content-between align-items-center font-list">
            อุณหภูมิ
            <span class="badge bg-primary rounded-pill font-list">{{ temp }}</span>
        </li>
        <li class="list-group-item d-flex justify-content-between align-items-center font-list">
            ความชื้น
            <span class="badge bg-primary rounded-pill font-list">{{ hum }}</span>
        </li>
        <li class="list-group-item d-flex justify-content-between align-items-center font-list">
            CO2
            <span class="badge bg-primary rounded-pill font-list">{{ co2 }}</span>
        </li>
        <li class="list-group-item d-flex justify-content-between align-items-center font-list">
            EC
            <span class="badge bg-primary rounded-pill font-list">{{ ec }}</span>
        </li>
    </ul>


</template>


<script>
import mqtt from 'mqtt'

export default {
    data() {
        return {
            dateTime: null,
            polling: null,
            temp: 0,
            hum: 30,
            co2: 519,
            ec: 312,
            connection: {
                host: 'broker.emqx.io',
                port: 8083,
                endpoint: '/mqtt',
                clean: true, // Reserved session
                connectTimeout: 4000, // Time out
                reconnectPeriod: 4000, // Reconnection interval
                // Certification Information
                clientId: 'nuxtjs_jetson_nano_farm',
                username: '',
                password: '',
            },
            subscription: {
                topic: 'plant/dashboard/value',
                qos: 0,
            },
            receiveNews: '',
            client: {
                connected: false,
            },
            subscribeSuccess: false,
        };
    },
    methods: {
        createConnection() {
            // Connect string, and specify the connection method used through protocol
            // ws unencrypted WebSocket connection
            // wss encrypted WebSocket connection
            // mqtt unencrypted TCP connection
            // mqtts encrypted TCP connection
            // wxs WeChat mini app connection
            // alis Alipay mini app connection
            const { host, port, endpoint, ...options } = this.connection
            const connectUrl = `ws://${host}:${port}${endpoint}`
            try {
                this.client = mqtt.connect(connectUrl, options)
            } catch (error) {
                console.log('mqtt.connect error', error)
            }
            this.client.on('connect', () => {
                console.log('Connection succeeded!')
                this.doSubscribe()
            })
            this.client.on('error', error => {
                console.log('Connection failed', error)
            })
            this.client.on('message', (topic, message) => {
                this.receiveNews = this.receiveNews.concat(message)
                console.log(`Received message ${message} from topic ${topic}`)

                if (topic == "plant/dashboard/value") {
                    var string = new TextDecoder().decode(message);
                    var arrystr = string.split(" ")
                    this.temp = arrystr[1]
                    this.hum = arrystr[2]
                    this.co2 = arrystr[3]
                    this.ec = arrystr[4]
                }
            })
        },
        doSubscribe() {
            const { topic, qos } = this.subscription
            this.client.subscribe(topic, { qos }, (error, res) => {
                if (error) {
                    console.log('Subscribe to topics error', error)
                    return
                }
                this.subscribeSuccess = true
                console.log('Subscribe to topics res', res)
            })
        },

        currentDateTime() {
            const current = new Date();
            const date = current.getFullYear() + "-" + (current.getMonth() + 1) + "-" + current.getDate();
            const time = current.getHours() + ":" + current.getMinutes();
            const dateTime = date + " " + time;
            return dateTime;
        }
    },
    created() {
        this.createConnection()
        // get date time
        this.polling = setInterval(() => {
            this.dateTime = this.currentDateTime();
        }, 1000);
    },
    beforeDestroy() {
        clearInterval(this.polling);
    }
};
</script>


<style scoped>
.content {
    padding: 30px 30px 30px 30px;
}

#contain {
    margin-top: 10px;
}

.font-list {
    font-size: 36px;
}
</style>
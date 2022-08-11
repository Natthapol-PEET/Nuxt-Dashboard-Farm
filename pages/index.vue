<template>
	<div class="center">
		<div class="card shadow" style="width: 1200px; height: 700px;">
			<div class="card-body">

				<!-- Header -->
				<div class="row align-items-start header">
					<div class="col-2">
						<Logoku />
					</div>
					<div class="col">
						<b style="font-size:48px">นันธิดา บ้านสวนฟาร์ม</b>
					</div>
				</div>

				<!-- Content -->
				<Content v-if="isPage == 0" :payload-message="payloadMessageSensor" />
				<PreviewImage v-if="isPage == 1" :item="payloadMessageImage" />

			</div>
		</div>
	</div>
</template>


<script>
import mqtt from 'mqtt';
import { v4 as uuidv4 } from 'uuid';

export default {
	data() {
		return {
			isPage: 0,
			payloadMessageSensor: '',
			payloadMessageImage: {
				"status": "end",
				"originalImage": "",
				"ndviImage": "",
			},
			connection: {
				// host: 'broker.emqx.io',
				host: '192.168.1.100',
				port: 8083,
				endpoint: '/mqtt',
				clean: true, // Reserved session
				connectTimeout: 4000, // Time out
				reconnectPeriod: 4000, // Reconnection interval
				// Certification Information
				clientId: `nuxtjs_farm_${uuidv4()}`,
				username: '',
				password: '',
			},
			subscription: [
				{
					topic: 'plant/dashboard/value',
					qos: 0,
				},
				{
					topic: 'plant/dashboard/image',
					qos: 0,
				},
			],
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
				// console.log(`Received message ${message} from topic ${topic}`)

				if (topic == "plant/dashboard/value") {
					this.payloadMessageSensor = message.toString();
				}
				else if (topic == "plant/dashboard/image") {
					const jsonString = Buffer.from(message)
					const parseData = JSON.parse(jsonString)
					this.payloadMessageImage = parseData

					this.isPage = 1

					if (parseData.status == 'end') {
						setTimeout(() => {
							this.isPage = 0
						}, 10000)
					}
				}
			})
		},
		doSubscribe() {
			var qos = 0;
			this.subscription.forEach((sub) => {
				this.client.subscribe(sub.topic, { qos }, (error, res) => {
					if (error) {
						console.log('Subscribe to topics error', error)
						return
					}
					this.subscribeSuccess = true
					console.log('Subscribe to topics res', res)
				})
			});
		},
	},
	created() {
		// init mqtt
		this.createConnection()
	},
	beforeDestroy() { },
	
};
</script>


<style>
.header {
	padding-top: 10px;
}

.center {
  margin: auto;
  /* width: 50%; */
  /* padding: 10px; */
}
</style>

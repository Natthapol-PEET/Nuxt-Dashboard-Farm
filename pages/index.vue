<template>
	<div class="container text-center">
		<div class="card shadow" style="width: 950px; height: 590px;">
			<div class="card-body">

				<!-- Header -->
				<div class="row align-items-start header">
					<div class="col-2">
						<Logo />
					</div>
					<div class="col">
						<b style="font-size:48px">นันธิดา บ้านสวนฟาร์ม</b>
					</div>
				</div>

				<!-- Content -->
				<Content :payload-message="payloadMessage" />

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
			payloadMessage: '',
			connection: {
				host: 'broker.emqx.io',
				port: 8083,
				endpoint: '/mqtt',
				clean: true, // Reserved session
				connectTimeout: 4000, // Time out
				reconnectPeriod: 4000, // Reconnection interval
				// Certification Information
				clientId: `nuxtjs_jetson_nano_farm-${uuidv4()}`,
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
					this.payloadMessage = message.toString();
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
	},
	created() {
		// init mqtt
		this.createConnection()
	},
	beforeDestroy() { }
};
</script>


<style scoped>
.header {
	padding-top: 10px;
}
</style>

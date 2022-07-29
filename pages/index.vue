<template>
	<div class="container text-center">
		<div class="card shadow">
			<div class="card-body">

				<!-- Header -->
				<div class="row align-items-start">
					<div class="col-2">
						<Logo />
					</div>
					<div class="col">
						<h2>Name farm</h2>
					</div>
				</div>

				<!-- Content -->
				<div class="row align-items-start">
					<div class="col">
						<Main />
					</div>
					<div class="col">
						<h2>{{ dateTime }}</h2>
					</div>
				</div>
				
			</div>
		</div>
	</div>
</template>


<script>
export default {
	data() {
		return {
			dateTime: null,
			polling: null
		}
	},
	methods: {
		currentDateTime() {
			const current = new Date();
			const date = current.getFullYear() + '-' + (current.getMonth() + 1) + '-' + current.getDate();
			const time = current.getHours() + ":" + current.getMinutes();
			const dateTime = date + ' ' + time;
			return dateTime;
		}
	},
	created() {
		this.polling = setInterval(() => {
			this.dateTime = this.currentDateTime()
		}, 1000)
	},
	beforeDestroy() {
		clearInterval(this.polling)
	}
};
</script>

<style scoped>
#logo {
	float: left;
}

#name {
	float: left;

}
</style>

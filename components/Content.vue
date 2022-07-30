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
export default {
    props: {
        payloadMessage: String,
    },
    watch: {
        payloadMessage: {
            immediate: true,
            deep: true,
            handler(newValue, oldValue) {
                var arrystr = newValue.split(" ")
                this.temp = arrystr[4]
                this.hum = arrystr[3]
                this.co2 = arrystr[1]
                this.ec = arrystr[2]
            }
        }
    },
    data() {
        return {
            dateTime: null,
            polling: null,
            temp: 0,
            hum: 0,
            co2: 0,
            ec: 0,
        };
    },
    methods: {
        currentDateTime() {
            const current = new Date();
            const date = current.getFullYear() + "-" + (current.getMonth() + 1) + "-" + current.getDate();
            const time = current.getHours() + ":" + current.getMinutes();
            const dateTime = date + " " + time;
            return dateTime;
        }
    },
    created() {
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
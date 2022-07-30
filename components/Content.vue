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
    data() {
        var arrystr = this.payloadMessage.split(" ")

        return {
            dateTime: null,
            polling: null,
            temp: arrystr[1],
            hum: arrystr[2],
            co2: arrystr[3],
            ec: arrystr[4],
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
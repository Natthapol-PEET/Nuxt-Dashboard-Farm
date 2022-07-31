<template>
    <div class="container text-center top-img-box">
        <div class="row align-items-start">
            <div class="col">
                <img v-if="originalImage != ''" id="img-size" :src="'data:image/jpeg;base64,' + originalImage">
                <h3 class="top-text-box">Original</h3>
            </div>
            <div class="col">
                <img v-if="ndviImage != ''" id="img-size" :src="'data:image/jpeg;base64,' + ndviImage">
                <h3 class="top-text-box">NDVI</h3>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        item: Object,
    },
    watch: {
        item: {
            immediate: true,
            deep: true,
            handler(newValue, oldValue) {
                this.status = newValue.status
                this.originalImage = newValue.originalImage
                this.ndviImage = newValue.ndviImage
            }
        }
    },
    data() {
        return {
            status: "end",  // next, end
            originalImage: "",
            ndviImage: "",
        }
    },
}
</script>

<style scoped>
.top-text-box {
    padding-top: 20px;
}
.top-img-box {
    padding-top: 30px;
}
#img-size {
    width: 400px;
    height: 300px;
}
</style>
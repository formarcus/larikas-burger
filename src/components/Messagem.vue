<template>
    <div class="messagem-container">
        <p>{{msg}}</p>
    </div>
</template>

<script>
export default {
    name: "messagem",
    props: {
        msg: String
    }
}
</script>

<style>
    .messagem-container {
        color: #004085;
        background-color: #cce5ff;
        border: 2px solid #88daff;
        border-radius: 5px;
        padding: 10px;
        max-width: 400px;
        margin: 30px auto;
    }
</style>
<template>
    <div class="rate">
        <input
            id="star5"
            :value="5"
            name="rate"
            type="radio"
            @change="$emit('update:modelValue', 5)"
        />
        <label for="star5" title="text">5 stars</label>
        <input
            id="star4"
            :value="4"
            name="rate"
            type="radio"
            @change="$emit('update:modelValue', 4)"
        />
        <label for="star4" title="text">4 stars</label>
        <input
            id="star3"
            :value="3"
            name="rate"
            type="radio"
            @change="$emit('update:modelValue', 3)"
        />
        <label for="star3" title="text">3 stars</label>
        <input
            id="star2"
            :value="2"
            name="rate"
            type="radio"
            @change="$emit('update:modelValue', 2)"
        />
        <label for="star2" title="text">2 stars</label>
        <input
            id="star1"
            :value="1"
            name="rate"
            type="radio"
            @change="$emit('update:modelValue', 1)"
        />
        <label for="star1" title="text">1 star</label>
    </div>
</template>

<script setup>
defineOptions({
    name: "StarRateInput",
});
</script>

<style scoped>
.rate {
    float: left;
    height: 46px;
    padding: 0 10px;
}

.rate:not(:checked) > input {
    position: absolute;
    top: -9999px;
}

.rate:not(:checked) > label {
    float: right;
    width: 1em;
    overflow: hidden;
    white-space: nowrap;
    cursor: pointer;
    font-size: 30px;
    color: #ccc;
}

.rate:not(:checked) > label:before {
    content: "★ ";
}

.rate > input:checked ~ label {
    color: #ffc700;
}

.rate:not(:checked) > label:hover,
.rate:not(:checked) > label:hover ~ label {
    color: #deb217;
}

.rate > input:checked + label:hover,
.rate > input:checked + label:hover ~ label,
.rate > input:checked ~ label:hover,
.rate > input:checked ~ label:hover ~ label,
.rate > label:hover ~ input:checked ~ label {
    color: #c59b08;
}

/* Modified from: https://github.com/mukulkant/Star-rating-using-pure-css */
</style>

<template>
  <b-carousel
      id="carousel-1"
      v-model="slide"
      :interval="4000"
      controls
      indicators
      fade
      background="#ababab"
      img-width="1024"
      img-height="220"
      style="text-shadow: 1px 1px 2px #333"
      @sliding-start="onSlideStart"
      @sliding-end="onSlideEnd"
  >
    <!-- Text slides with image -->
<!--    <b-carousel-slide-->
<!--        caption="First slide"-->
<!--        text="Nulla vitae elit libero, a pharetra augue mollis interdum."-->
<!--        img-src="@/assets/images/img4.jpg"-->
<!--    ></b-carousel-slide>-->

<!--    &lt;!&ndash; Slides with custom text &ndash;&gt;-->
<!--    <b-carousel-slide img-src="@/assets/images/img5.jpg">-->
<!--      <h1>Hello world!</h1>-->
<!--    </b-carousel-slide>-->
    <b-carousel-slide
        img-src="@/assets/images/img4.jpg"
    ></b-carousel-slide>
    <b-carousel-slide
        img-src="@/assets/images/img5.jpg"
    ></b-carousel-slide>
    <!-- Slides with image only -->
    <b-carousel-slide
        img-src="@/assets/images/img6.jpg"
    ></b-carousel-slide>

    <!-- Slides with img slot -->
  </b-carousel>
</template>

<script>
export default {
  name: "CarouselAnimation",

  data() {
    return {
      slide: 0,
      sliding: null,
    };
  },
  methods: {
    onSlideStart() {
      this.sliding = true;
    },
    onSlideEnd() {
      this.sliding = false;
    },
  },
};
</script>
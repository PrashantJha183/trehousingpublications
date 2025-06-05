<template>
  <div class="hero-section">
    <div
      class="carousel"
      @mouseenter="pauseAutoSlide"
      @mouseleave="startAutoSlide"
    >
      <div
        class="slide"
        v-for="(slide, index) in slides"
        :key="index"
        v-show="currentSlide === index"
      >
        <div class="content">
          <p class="subtitle">Examinations: The Key to Unlocking Your Future</p>
          <h1 class="title">{{ slide.title }}</h1>
          <p class="description">{{ slide.description }}</p>
          <button class="cta">Start Preparation</button>
        </div>
        <div class="image-container">
          <img :src="slide.image" class="main-image" alt="Student studying" />
        </div>
      </div>

      <!-- Navigation Arrows -->
      <button class="nav left" @click="prevSlide">&#8249;</button>
      <button class="nav right" @click="nextSlide">&#8250;</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      slides: [
        {
          title:
            "Get ready for 100+ government exams, covering both central and state opportunities.",
          description:
            "Access comprehensive resources designed for a wide range of exams including SSC, UPSC, Bihar Govt, and more. Stay updated with the latest syllabus and exam notifications to ensure you never miss important dates.",
          image: require("@/assets/Trehousingpublications_UI/hero.png"),
        },
        {
          title: "Ace your exams with expert guidance from top teachers.",
          description:
            "Learn from experienced educators through detailed courses, video tutorials, and step-by-step solutions. Our expert guidance ensures you understand key concepts and exam strategies to boost your confidence.",
          image: require("@/assets/Trehousingpublications_UI/hero2.png"),
        },
        {
          title: "Comprehensive study materials for your success.",
          description:
            "Explore well-researched books, updated syllabi, and previous year question papers to strengthen your preparation. Our materials are crafted to cover every important topic and maximize your learning efficiency.",
          image: require("@/assets/Trehousingpublications_UI/hero3.jpg"),
        },
        {
          title: "Join the best preparation platform for a brighter future.",
          description:
            "Be part of a thriving community focused on success through mock tests, live quizzes, and continuous support. Take the first step towards your dream job with resources trusted by thousands of aspirants.",
          image: require("@/assets/Trehousingpublications_UI/hero4.png"),
        },
      ],

      currentSlide: 0,
      autoSlideInterval: null,
    };
  },
  methods: {
    nextSlide() {
      this.currentSlide = (this.currentSlide + 1) % this.slides.length;
    },
    prevSlide() {
      this.currentSlide =
        (this.currentSlide - 1 + this.slides.length) % this.slides.length;
    },
    startAutoSlide() {
      this.autoSlideInterval = setInterval(this.nextSlide, 2000);
    },
    pauseAutoSlide() {
      clearInterval(this.autoSlideInterval);
    },
  },
  mounted() {
    this.startAutoSlide();
  },
};
</script>

<style scoped>
.hero-section {
  display: flex;
  justify-content: center;
  align-items: center;
  background: #fdf1f1;
  padding: 50px 0;
  width: 100%;
  overflow: hidden;
}

.carousel {
  display: flex;
  align-items: center;
  position: relative;
  width: 90%;
  max-width: 1200px;
  overflow: hidden;
  padding-left: 20px; /* Reduced padding */
  padding-right: 20px; /* Reduced padding */
}

.slide {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  flex-wrap: wrap;
}

.content {
  max-width: 50%;
  padding: 20px;
}

.subtitle {
  font-weight: bold;
  color: #333;
}

.title {
  font-size: 2rem;
  font-weight: bold;
}

.description {
  color: #666;
  margin: 10px 0;
}

.cta {
  background: blue;
  color: white;
  padding: 10px 20px;
  border: none;
  cursor: pointer;
  font-size: 1rem;
  border-radius: 5px;
}

.image-container {
  position: relative;
  max-width: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.main-image {
  width: 100%;
  height: 300px; /* Fixed height for all images */
  object-fit: cover; /* Ensures consistent image aspect ratio */
  margin-right: 40px;
}

.nav {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  background: none;
  color: black;
  border: none;
  font-size: 3rem;
  cursor: pointer;
  padding: 0;
  z-index: 1000;
  transition: all 0.3s ease-in-out;
}

.nav.left {
  left: 0px; /* Makes the left arrow more visible */
}

.nav.right {
  right: 0px; /* Makes the right arrow more visible */
}

@media (max-width: 768px) {
  .title {
    font-size: 1.5rem;
  }

  .cta {
    padding: 8px 16px;
    font-size: 0.9rem;
  }

  .content {
    max-width: 100%;
    text-align: center;
  }

  .image-container {
    max-width: 100%;
    margin-top: 20px;
    display: flex;
    justify-content: center; /* Center the image horizontally */
  }

  .main-image {
    width: 100%;
    height: 300px; /* Fixed height for consistency */
    object-fit: cover;
    margin-right: 0; /* Ensure there's no right margin on mobile */
  }

  .carousel {
    width: 100%;
    padding: 0 10px;
  }
}

@media (max-width: 480px) {
  .title {
    font-size: 1.2rem;
  }

  .cta {
    font-size: 0.8rem;
  }

  .carousel {
    width: 100%;
    padding: 0 5px;
  }

  .image-container {
    margin-top: 20px;
    display: flex;
    justify-content: center; /* Ensures image is centered */
  }

  .main-image {
    width: 100%;
    height: 250px; /* Adjust height for smaller screens */
    object-fit: cover;
  }
}
</style>

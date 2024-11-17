<template>
  <div class="movie-layout" v-if="movieData">
    <header class="movie-header">
      <div class="logo">TMDB</div>
      <nav class="nav-links" aria-label="header1">
        <a href="#">Movies</a>
        <a href="#">TV Shows</a>
        <a href="#">People</a>
        <a href="#">More</a>
      </nav>
      <div class="user-options">
        <button>
              <plus-icon class="icon" style="height: 20px" />

            </button>
        <button>EN</button>
        <button>Login</button>
        <button>Join TMDB</button>
        <button>
              <magnifying-glass-icon class="icon" style="height: 20px" />

            </button>
      </div>
    </header>
    <header class="movie-header2">
      <div>
        <nav class="secondary-header" aria-label="header2">
          <a href="#">Overview</a><play-icon class="icon" style="margin-left:-12px;height: 10px;  transform: rotate(90deg); color: black;"  />
          <a href="#">Media</a><play-icon class="icon" style="margin-left:-12px;height: 10px;  transform: rotate(90deg); color: black;"  />
          <a href="#">Phandom</a><play-icon class="icon" style="margin-left:-12px;height: 10px;  transform: rotate(90deg); color: black;"  />
          <a href="#">Share</a><play-icon class="icon" style="margin-left:-12px;height: 10px;  transform: rotate(90deg); color: black;"  />
        </nav>
      </div>
    </header>

    <main class="main-content">
      <div class="poster-section">
        <div>
          <img :src="movieData.Poster" alt="Movie Poster" class="poster-image" />
        </div>
        <div>
          <button class="watch-now-btn">
            <p>Now Streaming</p>
            <p>Watch Now</p>
          </button>
        </div>
      </div>

      <div class="movie-details">
        <h1 class="movie-title" ><span style="font-weight: 700;">{{ movieData.Title }}</span> ({{ movieData.Year }})</h1>
        <p class="release-info">
          {{ extractNumber(movieData.Rated) }} | {{ convertDate(movieData.Released) }} (KR) .
          {{ movieData.Genre }} . {{ convertToHours(movieData.Runtime) }}
        </p>
        <div class="movie-actions">
          <div class="ratings">
            <p class="imdb-rating">{{ movieData.imdbRating * 10 }}%</p>
            <span
              >User <br />
              Score</span
            >
            <list-bullet-icon class="icon" style="height: 20px" />
            <heart-icon class="icon" style="height: 20px" />
            <bookmark-icon class="icon" style="height: 20px" />
            <star-icon class="icon" style="height: 20px" />
            <play-icon class="icon" style="height: 20px" /> <span>Play Trailer</span>
          </div>
        </div>
        <div class="plot">
          <p>Obviously.</p>
          <h3>Overview</h3>
          <p>{{ movieData.Plot }}</p>
        </div>
        <div>
          <div class="grid-container">
            <div v-for="(item, index) in gridData" :key="index" class="grid-item">
              <p>
                <strong>{{ item.name }}</strong>
              </p>
              <p>{{ item.role }}</p>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import axios from 'axios'
import { HeartIcon, PlayIcon, StarIcon, BookmarkIcon, ListBulletIcon, PlusIcon, MagnifyingGlassIcon, } from '@heroicons/vue/24/solid'

export default {
  components: { HeartIcon, PlayIcon, StarIcon, BookmarkIcon, ListBulletIcon, PlusIcon,MagnifyingGlassIcon },
  name: 'MovieLayout',
  data() {
    return {
      movieData: null,
      gridData: [],
      rawData: {
        Director: "James Gunn",
        Writer: "James Gunn, Dan Abnett, Andy Lanning",
        Actors: "Chris Pratt, Zoe Saldana, Dave Bautista",
      },
    }
  },
  mounted() {
    this.fetchMovieData()
  },
  created() {
    // Process the raw string data into individual entities
    this.gridData = Object.entries(this.rawData).flatMap(([role, names]) =>
      names.split(",").map((name) => ({ role, name: name.trim() }))
    );
  },
  methods: {
    async fetchMovieData() {
      try {
        const response = await axios.get('http://www.omdbapi.com/?i=tt3896198&apikey=d2132124')
        this.movieData = response.data
      } catch (error) {
        console.error('Error fetching movie data:', error)
      }
    },
    convertToHours(time) {
      // Extract the number from the string
      const minutes = parseInt(time)
      if (isNaN(minutes)) return 'Invalid input'

      // Convert minutes to hours
      const hours = Math.floor(minutes / 60)
      const remainingMinutes = minutes % 60

      return `${hours}h ${remainingMinutes}m`
    },
    convertDate(dateStr) {
      try {
        // Create a Date object from the string
        const date = new Date(dateStr)

        if (isNaN(date.getTime())) {
          return 'Invalid date format'
        }

        // Extract day, month, and year
        const day = String(date.getDate()).padStart(2, '0')
        const month = String(date.getMonth() + 1).padStart(2, '0') // getMonth() returns 0-based index
        const year = date.getFullYear()

        // Return formatted string
        return `${day}/${month}/${year}`
      } catch (error) {
        return 'Error in conversion'
      }
    },
    extractNumber(str) {
      // Use a regular expression to extract the number
      const match = str.match(/\d+/)
      return match ? parseInt(match[0]) : 'No number found'
    },
  },
}
</script>

<style scoped>
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
  margin: 20px;
}

.grid-item {
  padding: 5px;
  border-radius: 8px;
  text-align: left;
}
.movie-layout {
  font-family: Arial, Helvetica, sans-serif;
  color: #ffffff;
  background-color: #0a2049;
}
.imdb-rating {
  font-size: 20px;
}
.movie.actions {
  display: inline;
}
.ratings {
  display: flex;
  gap: 40px;
}

.movie-header {
  display: flex ;
  justify-content: space-between;
  align-items: center;
  padding: 10px 20px;
  background-color: #0d253f;
}
.movie-header2 {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 10px;
  background-color: white;
}
.logo {
  color: #00d1b2;
  font-size: 1.5em;
  font-weight: bold;
}
.nav-links {
  display: flex;
  gap: 10px;
}
.nav-links a {
  margin: 0 15px;
  color: #ffffff;
  text-decoration: none;
}
.secondary-header a {
  margin: 0 15px;
  color: black;
  text-decoration: none;
}
.secondary-header {
  margin-top: 10px;
  background-color: #ffffff;
}

.user-options button {
  background-color: transparent;
  color: #ffffff;
  border: none;
  margin-left: 10px;
  cursor: pointer;
}
.user-options {
  display: flex;
  gap: 10px;
  margin-left: auto; /* Move options to the right */
}

.main-content {
  display: flex;
  padding: 20px;
}

.poster-section {
  width: 300px;
  position: relative;
}

.poster-image {
  border-radius: 8px;
}

.watch-now-btn {
  width: 300px;
  position: relative;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%);
  background-color: #032541;
  color: whitesmoke;
  border: none;
  padding: 15px 12px;
}
.movie-details {
  margin-left: 20px;
  max-width: 600px;
}

.movie-title {
  font-size: 32px;
  margin-bottom: 8px;
}

.release-info {
  color: #c0c0c0;
  margin-bottom: 16px;
}

.rating {
  color: #00d1b2;
}

.plot {
  margin-bottom: 20px;
}

.extra-details p {
  margin: 5px 0;
  color: #c0c0c0;
}

.extra-details strong {
  color: #ffffff;
}
</style>

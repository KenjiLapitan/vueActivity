<script>
export default {
  data() {
    return {
      search: '',
      filter: 'all',
      darkMode: false,
      notification: '',
      nextId: 8,

      newAnime: {
        title: '',
        genre: 'Action',
        status: 'Plan to Watch'
      },

      animeList: [
        {
          id: 1,
          title: 'Attack on Titan',
          genre: 'Action',
          status: 'Completed',
          episodes: 25,
          rating: 9.8,
          favorite: true,
          image: '/aot.webp',
          video: 'https://www.imdb.com/title/tt2560140'
        },

        {
          id: 2,
          title: 'Jujutsu Kaisen',
          genre: 'Action',
          status: 'Watching',
          episodes: 24,
          rating: 9.3,
          favorite: true,
          image: '/jjk.avif',
          video: 'https://www.imdb.com/title/tt12343534/'
        },

        {
          id: 3,
          title: 'Spy x Family',
          genre: 'Comedy',
          status: 'Watching',
          episodes: 25,
          rating: 8.9,
          favorite: false,
          image: '/sxf.jpg',
          video: 'https://www.imdb.com/title/tt13706018/'
        },

        {
          id: 4,
          title: 'Demon Slayer',
          genre: 'Fantasy',
          status: 'Completed',
          episodes: 26,
          rating: 9.2,
          favorite: true,
          image: '/ds.jpg',
          video: 'https://www.imdb.com/title/tt9335498/'
        },

        {
          id: 5,
          title: 'My Hero Academia',
          genre: 'Superhero',
          status: 'Plan to Watch',
          episodes: 13,
          rating: 8.6,
          favorite: false,
          image: '/mha.jpg',
          video: 'https://www.imdb.com/title/tt5626028/'
        },

        {
          id: 6,
          title: 'One Piece',
          genre: 'Adventure',
          status: 'Watching',
          episodes: 1120,
          rating: 9.7,
          favorite: true,
          image: '/op.jpg',
          video: 'https://www.imdb.com/title/tt0388629/'
        },

        {
          id: 7,
          title: 'Haikyuu!!',
          genre: 'Sports',
          status: 'Plan to Watch',
          episodes: 25,
          rating: 9.1,
          favorite: false,
          image: '/h.webp',
          video: 'https://www.imdb.com/title/tt3398540/'
        }
      ]
    }
  },

  computed: {
    filteredAnime() {
      const query = this.search.trim().toLowerCase()

      return this.animeList.filter((anime) => {
        const matchesSearch =
          !query ||
          anime.title.toLowerCase().includes(query) ||
          anime.genre.toLowerCase().includes(query)

        const matchesFilter =
          this.filter === 'all' ||
          (this.filter === 'favorites' && anime.favorite) ||
          anime.status === this.filter

        return matchesSearch && matchesFilter
      })
    },

    watchingCount() {
      return this.animeList.filter(
        (anime) => anime.status === 'Watching'
      ).length
    },

    favoriteCount() {
      return this.animeList.filter(
        (anime) => anime.favorite
      ).length
    },

    completedCount() {
      return this.animeList.filter(
        (anime) => anime.status === 'Completed'
      ).length
    }
  },

  methods: {
    toggleFavorite(anime) {
      anime.favorite = !anime.favorite

      this.showNotification(
        anime.favorite
          ? `${anime.title} added to favorites!`
          : `${anime.title} removed from favorites.`
      )

      this.saveList()
    },

    watchAnime(anime) {
      window.open(anime.video, '_blank')
    },

    removeAnime(id) {
      const anime = this.animeList.find(
        (item) => item.id === id
      )

      this.animeList = this.animeList.filter(
        (item) => item.id !== id
      )

      this.showNotification(
        `${anime.title} was removed from your list.`
      )

      this.saveList()
    },

    changeStatus(anime, event) {
      anime.status = event.target.value

      this.showNotification(
        `${anime.title} is now "${anime.status}".`
      )

      this.saveList()
    },

    addAnime() {
      const title = this.newAnime.title.trim()

      if (!title) {
        this.showNotification(
          'Please enter an anime title.'
        )

        return
      }

      this.animeList.unshift({
        id: this.nextId++,
        title: title,
        genre: this.newAnime.genre,
        status: this.newAnime.status,
        episodes: 0,
        rating: 0,
        favorite: false,

        image: '/omg.jpg'
      })

      this.newAnime.title = ''
      this.newAnime.genre = 'Action'
      this.newAnime.status = 'Plan to Watch'

      this.showNotification(
        `${title} was added to your watchlist!`
      )

      this.saveList()
    },

    showNotification(message) {
      this.notification = message

      window.clearTimeout(this.notificationTimer)

      this.notificationTimer = window.setTimeout(() => {
        this.notification = ''
      }, 2500)
    },

    saveList() {
      localStorage.setItem(
        'anime-watchlist',
        JSON.stringify(this.animeList)
      )
    },

    loadList() {
      const saved = localStorage.getItem('anime-watchlist')

      if (!saved) {
        return
      }

      try {
        const parsed = JSON.parse(saved)

        if (Array.isArray(parsed) && parsed.length > 0) {
          this.animeList = parsed
        }
      } catch {
        localStorage.removeItem('anime-watchlist')
      }
    }
  },

  mounted() {
      this.loadList()
    }
  }
</script>

<template>
  <div class="app" :class="{ dark: darkMode }">

    <!-- NAVIGATION -->
    <header class="topbar">

      <div class="brand">

        <div class="brand-mark">
          ★
        </div>

        <div>
          <h1>
            Libr<span>Anime</span>
          </h1>

          <p>
            Your personal anime library and tracker
          </p>
        </div>

      </div>

      <button class="theme-button" @click="darkMode = !darkMode">
        {{ darkMode ? '☀️ Light' : '🌙 Dark' }}
      </button>

    </header>


    <main class="container">

      <!-- HERO -->
      <section class="hero">

        <div>

          <span class="eyebrow">
            MY WATCHLIST
          </span>

          <h2>
            Track every story
            <br />
            <em>worth watching.</em>
          </h2>

          <p class="hero-copy">
            Keep your favorite anime organized,
            update your progress, and discover
            what to watch next.
          </p>

        </div>

        <div class="hero-orbit">

          <div class="orbit-card card-a">
            ⚔️
          </div>

          <div class="orbit-card card-b">
            🍜
          </div>

          <div class="orbit-card card-c">
            ✨
          </div>

          <div class="hero-star">
            ★
          </div>

        </div>

      </section>


      <!-- STATISTICS -->
      <section class="stats">

        <div class="stat-card">

          <span class="stat-icon purple">
            ◈
          </span>

          <div>
            <strong>
              {{ animeList.length }}
            </strong>

            <small>
              Total Anime
            </small>
          </div>

        </div>


        <div class="stat-card">

          <span class="stat-icon orange">
            ▶
          </span>

          <div>
            <strong>
              {{ watchingCount }}
            </strong>

            <small>
              Watching
            </small>
          </div>

        </div>


        <div class="stat-card">

          <span class="stat-icon pink">
            ♥
          </span>

          <div>
            <strong>
              {{ favoriteCount }}
            </strong>

            <small>
              Favorites
            </small>
          </div>

        </div>


        <div class="stat-card">

          <span class="stat-icon green">
            ✓
          </span>

          <div>
            <strong>
              {{ completedCount }}
            </strong>

            <small>
              Completed
            </small>
          </div>

        </div>

      </section>


      <!-- SEARCH AND FILTER -->
      <section class="toolbar">

        <div class="search-wrap">

          <span>
            ⌕
          </span>

          <input v-model="search" type="search" placeholder="Search anime or genre..." />

        </div>


        <div class="filters">

          <button :class="{ active: filter === 'all' }" @click="filter = 'all'">
            All
          </button>

          <button :class="{ active: filter === 'Watching' }" @click="filter = 'Watching'">
            Watching
          </button>

          <button :class="{ active: filter === 'Completed' }" @click="filter = 'Completed'">
            Completed
          </button>

          <button :class="{ active: filter === 'Plan to Watch' }" @click="filter = 'Plan to Watch'">
            Plan to Watch
          </button>

          <button :class="{ active: filter === 'favorites' }" @click="filter = 'favorites'">
            ♥ Favorites
          </button>

        </div>

      </section>


      <!-- ADD ANIME -->
      <section class="add-panel">

        <div class="panel-title">

          <div class="plus">
            +
          </div>

          <div>

            <h3>
              Add anime
            </h3>

            <p>
              Add something new to your watchlist.
            </p>

          </div>

        </div>


        <div class="add-form">

          <input v-model="newAnime.title" @keyup.enter="addAnime" type="text" placeholder="Anime title" />


          <select v-model="newAnime.genre">

            <option>
              Action
            </option>

            <option>
              Adventure
            </option>

            <option>
              Comedy
            </option>

            <option>
              Fantasy
            </option>

            <option>
              Romance
            </option>

            <option>
              Sports
            </option>

            <option>
              Superhero
            </option>

          </select>


          <select v-model="newAnime.status">

            <option>
              Plan to Watch
            </option>

            <option>
              Watching
            </option>

            <option>
              Completed
            </option>

          </select>


          <button class="add-button" @click="addAnime">
            + Add Anime
          </button>

        </div>

      </section>


      <!-- LIST HEADER -->
      <section class="list-header">

        <div>

          <h3>
            My Anime
          </h3>

          <p>
            {{ filteredAnime.length }}
            title<span v-if="filteredAnime.length !== 1">s</span>
            shown
          </p>

        </div>

      </section>


      <!-- ANIME CARDS -->
      <section v-if="filteredAnime.length > 0" class="anime-grid">

        <article v-for="anime in filteredAnime" :key="anime.id" class="anime-card">

          <div class="poster">

            <img :src="anime.image" :alt="anime.title + ' cover'" />


            <button class="favorite" :class="{ selected: anime.favorite }" @click="toggleFavorite(anime)">
              {{ anime.favorite ? '♥' : '♡' }}
            </button>


            <span class="rating" v-if="anime.rating > 0">
              ★ {{ anime.rating }}
            </span>


            <span class="rating unrated" v-else>
              New
            </span>

          </div>


          <div class="anime-info">

            <div class="genre">
              {{ anime.genre }}
            </div>

            <h4>
              {{ anime.title }}
            </h4>

            <p>
              {{ anime.episodes }} episodes
            </p>


            <div class="status-row">

              <label>
                Status
              </label>

              <select :value="anime.status" @change="changeStatus(anime, $event)">

                <option>
                  Plan to Watch
                </option>

                <option>
                  Watching
                </option>

                <option>
                  Completed
                </option>

              </select>

            </div>


            <button class="watch-button" @click="watchAnime(anime)">
              ▶ Watch
            </button>


            <button class="remove-button" @click="removeAnime(anime.id)">
              Remove
            </button>

          </div>

        </article>

      </section>


      <!-- EMPTY STATE -->
      <section v-else class="empty-state">

        <div class="empty-icon">
          ⌕
        </div>


        <h3 v-if="search">
          No anime found
        </h3>

        <h3 v-else-if="filter === 'favorites'">
          No favorites yet
        </h3>

        <h3 v-else>
          Nothing here yet
        </h3>


        <p v-if="search">
          Try a different title or genre.
        </p>

        <p v-else-if="filter === 'favorites'">
          Tap the heart on an anime
          to save it as a favorite.
        </p>

        <p v-else>
          Add your first anime above
          to get started.
        </p>


        <button v-if="filter !== 'all' || search" @click="search = ''; filter = 'all'">
          Show All Anime
        </button>

        <span v-else>
          Start building your list above ↑
        </span>

      </section>


      <footer>

        <span>
          Built with Vue.js 3
        </span>

        <span>
          •
        </span>

        <span>
          Changes are saved automatically
        </span>

      </footer>

    </main>


    <!-- NOTIFICATION -->
    <transition name="toast">

      <div v-if="notification" class="toast">
        {{ notification }}
      </div>

    </transition>

  </div>
</template>

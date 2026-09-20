```vue
<template>
  <main class="meetups-page">
    <!-- =========================================================
         HEADER + SEARCH
         ========================================================= -->
    <section class="meetups-header">
      <div class="meetups-container">
        <div class="header-content">
          <span class="eyebrow">Sundarbans House · Community</span>

          <h1>
            Find your
            <span>people.</span>
          </h1>

          <p>
            Discover meetups, find your city chapter, and connect with
            people from the Sundarbans community.
          </p>
        </div>

        <!-- SEARCH -->
        <div class="search-box">
          <Search :size="20" />

          <input
            v-model="searchQuery"
            type="search"
            placeholder="Search anything..."
            aria-label="Search meetups"
          />

          <span v-if="searchQuery" class="search-count">
            {{ filteredMeetups.length }}
          </span>

          <button
            v-if="searchQuery"
            type="button"
            class="clear-button"
            aria-label="Clear search"
            @click="clearSearch"
          >
            <X :size="17" />
          </button>
        </div>

        <!-- =====================================================
             CITY SELECTOR
             ===================================================== -->
        <div class="city-area">
          <div class="city-heading">
            <span>Choose your city</span>

            <button
              v-if="selectedCity"
              type="button"
              @click="clearCity"
            >
              Clear
            </button>
          </div>

          <div class="city-pills">
            <button
              type="button"
              class="city-pill"
              :class="{ active: !selectedCity }"
              @click="clearCity"
            >
              All
            </button>

            <button
              v-for="city in cities"
              :key="city.key"
              type="button"
              class="city-pill"
              :class="{ active: selectedCity === city.key }"
              @click="selectCity(city.key)"
            >
              {{ city.name }}
            </button>
          </div>
        </div>
      </div>
    </section>

    <!-- =========================================================
         WHAT ARE YOU LOOKING FOR?
         ========================================================= -->
    <section class="looking-section">
      <div class="meetups-container">
        <div class="section-heading">
          <span class="eyebrow">Explore</span>

          <h2>What are you looking for?</h2>

          <p>
            Pick an interest and discover relevant community activity.
          </p>
        </div>

        <div class="interest-grid">
          <button
            v-for="item in interests"
            :key="item.key"
            type="button"
            class="interest-card"
            :class="{ active: activeInterest === item.key }"
            @click="toggleInterest(item.key)"
          >
            <div class="interest-icon">
              <component :is="item.icon" :size="21" />
            </div>

            <div class="interest-text">
              <strong>{{ item.title }}</strong>
              <span>{{ item.description }}</span>
            </div>

            <ArrowUpRight :size="16" class="interest-arrow" />
          </button>
        </div>
      </div>
    </section>

    <!-- =========================================================
         UPCOMING MEETUPS
         ========================================================= -->
    <section class="upcoming-section">
      <div class="meetups-container">
        <div class="section-heading section-heading-row">
          <div>
            <span class="eyebrow">What's next</span>
            <h2>Upcoming meetups</h2>
          </div>

          <span v-if="upcomingMeetups.length" class="result-label">
            {{ upcomingMeetups.length }}
            {{ upcomingMeetups.length === 1 ? 'event' : 'events' }}
          </span>
        </div>

        <!-- EVENTS -->
        <div
          v-if="upcomingMeetups.length"
          class="upcoming-grid"
        >
          <article
            v-for="event in upcomingMeetups"
            :key="event.key"
            class="event-card"
          >
            <div class="event-top">
              <span>{{ event.city }}</span>

              <span class="upcoming-badge">
                <i></i>
                Upcoming
              </span>
            </div>

            <h3>{{ event.title }}</h3>

            <p v-if="event.description">
              {{ event.description }}
            </p>

            <div class="event-info">
              <span v-if="event.date">
                <CalendarDays :size="15" />
                {{ event.date }}
              </span>

              <span v-if="event.time">
                <Clock3 :size="15" />
                {{ event.time }}
              </span>

              <span v-if="event.location">
                <MapPin :size="15" />
                {{ event.location }}
              </span>
            </div>

            <div class="event-bottom">
              <RouterLink
                :to="`/meetups/${event.slug}`"
                class="primary-button"
              >
                Explore
                <ArrowRight :size="15" />
              </RouterLink>

              <a
                v-if="event.url"
                :href="event.url"
                target="_blank"
                rel="noopener noreferrer"
                class="event-url"
              >
                Details
                <ExternalLink :size="13" />
              </a>
            </div>
          </article>
        </div>

        <!-- NO UPCOMING EVENTS -->
        <div v-else class="no-events">
          <div class="no-events-icon">
            <CalendarDays :size="19" />
          </div>

          <div class="no-events-content">
            <strong>No upcoming meetups yet</strong>

            <span>
              There are no future meetups published at the moment.
            </span>
          </div>

          <button
            type="button"
            class="text-button"
            @click="showPastMeetups = true"
          >
            Explore past meetups
            <ArrowRight :size="14" />
          </button>
        </div>
      </div>
    </section>

    <!-- =========================================================
         SUGGEST A MEETUP
         ========================================================= -->
    <section class="suggest-section">
      <div class="meetups-container">
        <div class="suggest-card">
          <div>
            <span class="eyebrow">Your idea could be the next one</span>

            <h2>
              Don't see something
              <span>near you?</span>
            </h2>

            <p>
              Suggest a meetup, gathering or city chapter for the community.
            </p>
          </div>

          <a
            href="https://forms.gle/iHeYQsAbsUTBHJJC6"
            target="_blank"
            rel="noopener noreferrer"
            class="primary-button"
          >
            Suggest a meetup
            <ArrowUpRight :size="16" />
          </a>
        </div>
      </div>
    </section>

    <!-- =========================================================
         PAST MEETUPS
         Hidden until explicitly opened.
         ========================================================= -->
    <Transition name="past">
      <section
        v-if="showPastMeetups"
        class="past-section"
      >
        <div class="meetups-container">
          <div class="section-heading section-heading-row">
            <div>
              <span class="eyebrow">Community history</span>
              <h2>Past meetups</h2>
            </div>

            <button
              type="button"
              class="hide-button"
              @click="showPastMeetups = false"
            >
              Hide
              <X :size="14" />
            </button>
          </div>

          <!-- SEARCH RESULT MESSAGE -->
          <div
            v-if="searchQuery || selectedCity || activeInterest"
            class="active-filters"
          >
            <span>Showing results for your selection</span>

            <button
              type="button"
              @click="resetFilters"
            >
              Reset
            </button>
          </div>

          <!-- PAST LIST -->
          <div
            v-if="filteredMeetups.length"
            class="past-list"
          >
            <article
              v-for="meetup in visiblePastMeetups"
              :key="meetup.key"
              class="past-card"
            >
              <div
                v-if="meetup.date"
                class="past-date"
              >
                <strong>{{ meetup.day }}</strong>
                <span>{{ meetup.month }}</span>
              </div>

              <div class="past-content">
                <span class="past-city">
                  {{ meetup.city }}
                </span>

                <h3>{{ meetup.title }}</h3>

                <p v-if="meetup.description">
                  {{ meetup.description }}
                </p>

                <div class="past-info">
                  <span v-if="meetup.location">
                    <MapPin :size="13" />
                    {{ meetup.location }}
                  </span>

                  <span v-if="meetup.attendance">
                    <Users :size="13" />
                    {{ meetup.attendance }} attended
                  </span>
                </div>
              </div>

              <a
                v-if="meetup.url"
                :href="meetup.url"
                target="_blank"
                rel="noopener noreferrer"
                class="past-link"
                aria-label="Open meetup details"
              >
                <ExternalLink :size="16" />
              </a>
            </article>
          </div>

          <!-- NO RESULTS -->
          <div v-else class="empty-results">
            <SearchX :size="19" />

            <span>
              No meetups match your current search.
            </span>

            <button
              type="button"
              @click="resetFilters"
            >
              Clear filters
            </button>
          </div>
        </div>
      </section>
    </Transition>
  </main>
</template>

<script setup>
import { computed, ref } from 'vue';

import {
  ArrowRight,
  ArrowUpRight,
  BookOpen,
  CalendarDays,
  Clock3,
  Code2,
  Coffee,
  ExternalLink,
  MapPin,
  Search,
  SearchX,
  Users,
  UsersRound,
  X,
} from 'lucide-vue-next';

import { regionConfigs } from './meetups/regionConfigs.js';

/* ============================================================
   STATE
   ============================================================ */

const searchQuery = ref('');
const selectedCity = ref('');
const activeInterest = ref('');
const showPastMeetups = ref(false);

/* ============================================================
   CITIES
   ============================================================ */

const cities = [
  {
    key: 'bangalore',
    slug: 'bangalore',
    name: 'Bangalore',
  },
  {
    key: 'chandigarh',
    slug: 'chandigarh',
    name: 'Chandigarh',
  },
  {
    key: 'chennai',
    slug: 'chennai',
    name: 'Chennai',
  },
  {
    key: 'delhi',
    slug: 'delhi-ncr',
    name: 'Delhi NCR',
  },
  {
    key: 'hyderabad',
    slug: 'hyderabad',
    name: 'Hyderabad',
  },
  {
    key: 'kolkata',
    slug: 'kolkata',
    name: 'Kolkata',
  },
  {
    key: 'mumbai',
    slug: 'mumbai',
    name: 'Mumbai',
  },
  {
    key: 'patna',
    slug: 'patna',
    name: 'Patna',
  },
];

/* ============================================================
   INTERESTS
   ============================================================ */

const interests = [
  {
    key: 'learn',
    title: 'Learn',
    description: 'Study, talks and knowledge sharing',
    icon: BookOpen,
    words: [
      'learn',
      'study',
      'academic',
      'education',
      'talk',
      'session',
    ],
  },

  {
    key: 'build',
    title: 'Build',
    description: 'Technology, projects and collaboration',
    icon: Code2,
    words: [
      'tech',
      'technology',
      'project',
      'coding',
      'developer',
      'hack',
    ],
  },

  {
    key: 'social',
    title: 'Meet people',
    description: 'Hangouts and conversations',
    icon: Coffee,
    words: [
      'social',
      'hangout',
      'casual',
      'coffee',
      'fun',
      'meet',
    ],
  },

  {
    key: 'community',
    title: 'Community',
    description: 'Find your local chapter',
    icon: UsersRound,
    words: [],
  },
];

/* ============================================================
   HELPERS
   ============================================================ */

function normalize(value) {
  return String(value ?? '')
    .toLowerCase()
    .normalize('NFD')
    .replace(/[\u0300-\u036f]/g, '')
    .replace(/[^\w\s-]/g, ' ')
    .replace(/\s+/g, ' ')
    .trim();
}

function parseDate(value) {
  if (!value) return null;

  const date = new Date(value);

  return Number.isNaN(date.getTime())
    ? null
    : date;
}

function dateParts(value) {
  const date = parseDate(value);

  if (!date) {
    return {
      day: '',
      month: '',
    };
  }

  return {
    day: new Intl.DateTimeFormat('en-IN', {
      day: '2-digit',
    }).format(date),

    month: new Intl.DateTimeFormat('en-IN', {
      month: 'short',
    }).format(date),
  };
}

function getArray(value) {
  return Array.isArray(value)
    ? value
    : [];
}

function getText(...values) {
  return values.find(
    (value) =>
      value !== undefined &&
      value !== null &&
      String(value).trim() !== ''
  ) || '';
}

/* ============================================================
   NORMALIZE REGION DATA
   ============================================================ */

const allMeetups = computed(() => {
  const result = [];

  cities.forEach((city) => {
    const config = regionConfigs?.[city.key];

    if (!config) return;

    const records = getArray(
      config.pastMeetups
    );

    records.forEach((item, index) => {
      const parts = dateParts(item.date);

      result.push({
        key:
          item.id ??
          `${city.key}-${index}`,

        city: city.name,
        cityKey: city.key,
        slug: city.slug,

        title: getText(
          item.title,
          item.name,
          `${city.name} Meetup`
        ),

        description: getText(
          item.about,
          item.description,
          item.summary
        ),

        date: getText(
          item.date,
          item.eventDate
        ),

        dateObject: parseDate(
          item.date ?? item.eventDate
        ),

        day: parts.day,
        month: parts.month,

        time: getText(
          item.time,
          item.eventTime
        ),

        location: getText(
          item.location,
          item.venue,
          item.address1,
          item.address
        ),

        attendance: getText(
          item.attended,
          item.attendance,
          item.attendees
        ),

        url: getText(
          item.instaUrl,
          item.instagram,
          item.url,
          item.link
        ),

        tags: getArray(item.tags),
      });
    });
  });

  return result;
});

/* ============================================================
   FLEXIBLE SEARCH
   ============================================================ */

function matchesSearch(meetup, query) {
  if (!query) return true;

  const searchable = normalize(
    [
      meetup.title,
      meetup.city,
      meetup.location,
      meetup.description,
      meetup.attendance,
      ...meetup.tags,
    ].join(' ')
  );

  /*
   * Every word entered by the user must exist somewhere
   * in the meetup information.
   *
   * Examples:
   *
   * "bangalore"
   * "bang"
   * "tech"
   * "bangalore tech"
   * "coffee kolkata"
   */
  const words = normalize(query)
    .split(' ')
    .filter(Boolean);

  return words.every((word) =>
    searchable.includes(word)
  );
}

/* ============================================================
   FILTERED MEETUPS
   ============================================================ */

const filteredMeetups = computed(() => {
  let result = [...allMeetups.value];

  /* CITY */
  if (selectedCity.value) {
    result = result.filter(
      (item) =>
        item.cityKey === selectedCity.value
    );
  }

  /* SEARCH */
  if (searchQuery.value.trim()) {
    result = result.filter((item) =>
      matchesSearch(
        item,
        searchQuery.value
      )
    );
  }

  /* INTEREST */
  if (activeInterest.value) {
    const interest = interests.find(
      (item) =>
        item.key === activeInterest.value
    );

    if (
      interest &&
      interest.words.length
    ) {
      result = result.filter((item) => {
        const searchable = normalize(
          [
            item.title,
            item.description,
            item.location,
            ...item.tags,
          ].join(' ')
        );

        return interest.words.some(
          (word) =>
            searchable.includes(
              normalize(word)
            )
        );
      });
    }
  }

  /* NEWEST FIRST */
  result.sort((a, b) => {
    const first =
      a.dateObject?.getTime() ?? -Infinity;

    const second =
      b.dateObject?.getTime() ?? -Infinity;

    return second - first;
  });

  return result;
});

const visiblePastMeetups = computed(() =>
  filteredMeetups.value.slice(0, 12)
);

/* ============================================================
   UPCOMING MEETUPS
   ============================================================ */

const upcomingMeetups = computed(() => {
  const now = new Date();

  now.setHours(0, 0, 0, 0);

  const result = [];

  cities.forEach((city) => {
    const config =
      regionConfigs?.[city.key];

    if (!config?.upcoming) return;

    const event = config.upcoming;

    const eventDate = parseDate(
      event.date
    );

    /*
     * Never show an event as upcoming
     * if its date has already passed.
     */
    if (
      eventDate &&
      eventDate < now
    ) {
      return;
    }

    result.push({
      key: `${city.key}-upcoming`,

      city: city.name,
      slug: city.slug,

      title: getText(
        event.title,
        event.name,
        `${city.name} Meetup`
      ),

      description: getText(
        event.about,
        event.description,
        event.summary
      ),

      date: getText(
        event.date,
        event.eventDate
      ),

      time: getText(
        event.time,
        event.eventTime
      ),

      location: getText(
        event.location,
        event.venue,
        event.address1,
        event.address
      ),

      url: getText(
        event.instaUrl,
        event.instagram,
        event.url,
        event.link
      ),
    });
  });

  return result.sort((a, b) => {
    const first =
      parseDate(a.date)?.getTime() ??
      Infinity;

    const second =
      parseDate(b.date)?.getTime() ??
      Infinity;

    return first - second;
  });
});

/* ============================================================
   ACTIONS
   ============================================================ */

function selectCity(city) {
  selectedCity.value =
    selectedCity.value === city
      ? ''
      : city;

  /*
   * Only show historical results if the
   * user is actually interacting with them.
   */
  showPastMeetups.value = false;
}

function clearCity() {
  selectedCity.value = '';
}

function clearSearch() {
  searchQuery.value = '';
}

function toggleInterest(key) {
  activeInterest.value =
    activeInterest.value === key
      ? ''
      : key;

  /*
   * Community is primarily a city-discovery
   * action, so don't dump the archive.
   */
  if (
    activeInterest.value &&
    activeInterest.value !== 'community' &&
    filteredMeetups.value.length
  ) {
    showPastMeetups.value = true;

    requestAnimationFrame(() => {
      document
        .querySelector('.past-section')
        ?.scrollIntoView({
          behavior: 'smooth',
          block: 'start',
        });
    });
  } else {
    showPastMeetups.value = false;
  }
}

function resetFilters() {
  searchQuery.value = '';
  selectedCity.value = '';
  activeInterest.value = '';
}
</script>

<style scoped>
/* ============================================================
   BASE
   ============================================================ */

.meetups-page {
  min-height: 100vh;
  background: var(--bg);
}

.meetups-container {
  width: min(1180px, calc(100% - 40px));
  margin: 0 auto;
}

/* ============================================================
   HEADER
   ============================================================ */

.meetups-header {
  padding: clamp(7rem, 12vw, 9rem) 0 2.7rem;
  border-bottom: 1px solid var(--border);
}

.header-content {
  max-width: 800px;
}

.eyebrow {
  display: inline-block;
  color: var(--accent);
  font-size: 0.69rem;
  font-weight: 600;
  letter-spacing: 0.15em;
  text-transform: uppercase;
}

.header-content h1 {
  margin-top: 0.65rem;
  font-family: var(--font-display);
  font-size: clamp(3rem, 7vw, 6rem);
  line-height: 0.94;
  letter-spacing: -0.045em;
  color: var(--text);
}

.header-content h1 span {
  color: var(--accent);
}

.header-content p {
  max-width: 650px;
  margin-top: 1.4rem;
  color: var(--text2);
  font-size: 0.92rem;
  line-height: 1.75;
}

/* ============================================================
   SEARCH
   ============================================================ */

.search-box {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  min-height: 60px;
  margin-top: 2.3rem;
  padding: 0 1.1rem;
  color: var(--text2);
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
  box-shadow: var(--shadow-sm);
}

.search-box:focus-within {
  border-color: var(--border-gold);
}

.search-box input {
  width: 100%;
  min-width: 0;
  border: 0;
  outline: 0;
  color: var(--text);
  background: transparent;
  font-family: var(--font-body);
  font-size: 0.88rem;
}

.search-box input::placeholder {
  color: var(--text3);
}

.search-count {
  color: var(--accent);
  font-size: 0.7rem;
}

.clear-button {
  display: grid;
  place-items: center;
  width: 30px;
  height: 30px;
  flex: none;
  padding: 0;
  border: 0;
  border-radius: 50%;
  color: var(--text2);
  background: transparent;
  cursor: pointer;
}

.clear-button:hover {
  color: var(--text);
  background: var(--surface2);
}

/* ============================================================
   CITY
   ============================================================ */

.city-area {
  margin-top: 1rem;
}

.city-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 0.65rem;
}

.city-heading span {
  color: var(--text3);
  font-size: 0.66rem;
  font-weight: 600;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.city-heading button {
  padding: 0;
  border: 0;
  color: var(--accent);
  background: transparent;
  font-size: 0.68rem;
  cursor: pointer;
}

.city-pills {
  display: flex;
  gap: 0.45rem;
  overflow-x: auto;
  padding-bottom: 0.25rem;
  scrollbar-width: none;
}

.city-pills::-webkit-scrollbar {
  display: none;
}

.city-pill {
  flex: none;
  padding: 0.55rem 0.85rem;
  color: var(--text2);
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 999px;
  font-size: 0.7rem;
  cursor: pointer;
  transition: 0.2s ease;
}

.city-pill:hover {
  color: var(--text);
  border-color: var(--border-card-hover);
}

.city-pill.active {
  color: var(--bg);
  background: var(--accent);
  border-color: var(--accent);
}

/* ============================================================
   SECTIONS
   ============================================================ */

.looking-section,
.past-section {
  background: var(--bg2);
}

.looking-section,
.upcoming-section,
.suggest-section,
.past-section {
  padding: clamp(4rem, 7vw, 6rem) 0;
}

.section-heading {
  margin-bottom: 1.7rem;
}

.section-heading-row {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  gap: 1rem;
}

.section-heading h2 {
  margin-top: 0.4rem;
  font-family: var(--font-display);
  font-size: clamp(1.9rem, 4vw, 3rem);
  line-height: 1.05;
  color: var(--text);
}

.section-heading p {
  max-width: 580px;
  margin-top: 0.55rem;
  color: var(--text2);
  font-size: 0.78rem;
  line-height: 1.6;
}

.result-label {
  color: var(--text3);
  font-size: 0.72rem;
}

/* ============================================================
   INTERESTS
   ============================================================ */

.interest-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 0.7rem;
}

.interest-card {
  position: relative;
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
  min-height: 140px;
  padding: 1.15rem;
  text-align: left;
  color: var(--text);
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
  cursor: pointer;
  transition:
    transform 0.25s ease,
    border-color 0.25s ease,
    background 0.25s ease;
}

.interest-card:hover,
.interest-card.active {
  transform: translateY(-2px);
  background: var(--surface2);
  border-color: var(--border-gold);
}

.interest-icon {
  display: grid;
  place-items: center;
  width: 38px;
  height: 38px;
  flex: none;
  color: var(--accent);
  background: rgba(213, 166, 58, 0.08);
  border: 1px solid var(--border-gold);
  border-radius: var(--rad);
}

.interest-text strong {
  display: block;
  padding-right: 1rem;
  font-family: var(--font-display);
  font-size: 1rem;
}

.interest-text span {
  display: block;
  margin-top: 0.35rem;
  color: var(--text2);
  font-size: 0.7rem;
  line-height: 1.5;
}

.interest-arrow {
  position: absolute;
  top: 1rem;
  right: 1rem;
  color: var(--text3);
}

/* ============================================================
   UPCOMING
   ============================================================ */

.upcoming-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 0.8rem;
}

.event-card {
  padding: 1.4rem;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
  transition:
    transform 0.25s ease,
    border-color 0.25s ease;
}

.event-card:hover {
  transform: translateY(-2px);
  border-color: var(--border-card-hover);
}

.event-top {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
}

.event-top > span:first-child {
  color: var(--accent);
  font-size: 0.66rem;
  font-weight: 600;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.upcoming-badge {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  color: var(--text3);
  font-size: 0.63rem;
}

.upcoming-badge i {
  width: 5px;
  height: 5px;
  border-radius: 50%;
  background: var(--accent);
}

.event-card h3 {
  margin-top: 0.65rem;
  font-family: var(--font-display);
  font-size: 1.4rem;
  line-height: 1.15;
}

.event-card > p {
  margin-top: 0.55rem;
  color: var(--text2);
  font-size: 0.77rem;
  line-height: 1.6;
}

.event-info {
  display: flex;
  flex-wrap: wrap;
  gap: 0.65rem 1rem;
  margin-top: 1rem;
}

.event-info span {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  color: var(--text2);
  font-size: 0.68rem;
}

.event-bottom {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
  margin-top: 1.3rem;
  padding-top: 1rem;
  border-top: 1px solid var(--border);
}

.primary-button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 0.4rem;
  padding: 0.7rem 1rem;
  color: var(--bg);
  background: var(--accent);
  border: 1px solid var(--accent);
  border-radius: var(--rad);
  font-size: 0.7rem;
  font-weight: 600;
  text-decoration: none;
  cursor: pointer;
  transition: 0.2s ease;
}

.primary-button:hover {
  filter: brightness(1.08);
  transform: translateY(-1px);
}

.event-url {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  color: var(--text2);
  font-size: 0.69rem;
  text-decoration: none;
}

.event-url:hover {
  color: var(--accent);
}

/* ============================================================
   NO EVENTS
   ============================================================ */

.no-events {
  display: flex;
  align-items: center;
  gap: 0.85rem;
  padding: 1rem 1.2rem;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad);
}

.no-events-icon {
  display: grid;
  place-items: center;
  width: 38px;
  height: 38px;
  flex: none;
  color: var(--accent);
  background: rgba(213, 166, 58, 0.08);
  border-radius: var(--rad);
}

.no-events-content {
  display: flex;
  flex-direction: column;
  gap: 0.15rem;
  flex: 1;
}

.no-events-content strong {
  font-family: var(--font-display);
  font-size: 0.9rem;
}

.no-events-content span {
  color: var(--text2);
  font-size: 0.69rem;
}

.text-button {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  flex: none;
  padding: 0;
  color: var(--accent);
  border: 0;
  background: transparent;
  font-size: 0.68rem;
  cursor: pointer;
}

/* ============================================================
   SUGGEST
   ============================================================ */

.suggest-section {
  padding-top: 2rem;
  padding-bottom: 3rem;
}

.suggest-card {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 2rem;
  padding: clamp(2rem, 5vw, 3.5rem);
  background:
    linear-gradient(
      110deg,
      rgba(213, 166, 58, 0.08),
      transparent 65%
    ),
    var(--surface);
  border: 1px solid var(--border-gold);
  border-radius: var(--rad2);
}

.suggest-card h2 {
  max-width: 650px;
  margin-top: 0.45rem;
  font-family: var(--font-display);
  font-size: clamp(1.8rem, 4vw, 3rem);
  line-height: 1.05;
}

.suggest-card h2 span {
  color: var(--accent);
}

.suggest-card p {
  margin-top: 0.65rem;
  color: var(--text2);
  font-size: 0.78rem;
}

.suggest-card .primary-button {
  flex: none;
}

/* ============================================================
   PAST
   ============================================================ */

.hide-button {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  padding: 0.5rem 0.7rem;
  color: var(--text2);
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad);
  font-size: 0.68rem;
  cursor: pointer;
}

.hide-button:hover {
  color: var(--text);
  border-color: var(--border-card-hover);
}

.active-filters {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
  margin-bottom: 1rem;
  padding: 0.7rem 0.9rem;
  color: var(--text2);
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad);
  font-size: 0.69rem;
}

.active-filters button {
  color: var(--accent);
  border: 0;
  background: transparent;
  cursor: pointer;
  font-size: 0.68rem;
}

.past-list {
  display: flex;
  flex-direction: column;
  gap: 0.55rem;
}

.past-card {
  display: grid;
  grid-template-columns: 64px minmax(0, 1fr) auto;
  align-items: center;
  gap: 1rem;
  padding: 1rem;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
}

.past-date {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 60px;
  background: var(--bg);
  border: 1px solid var(--border);
  border-radius: var(--rad);
}

.past-date strong {
  font-family: var(--font-display);
  font-size: 1.2rem;
}

.past-date span {
  color: var(--accent);
  font-size: 0.6rem;
  text-transform: uppercase;
}

.past-city {
  color: var(--accent);
  font-size: 0.63rem;
  font-weight: 600;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.past-content h3 {
  margin-top: 0.2rem;
  font-family: var(--font-display);
  font-size: 1rem;
}

.past-content p {
  margin-top: 0.3rem;
  color: var(--text2);
  font-size: 0.7rem;
  line-height: 1.5;
}

.past-info {
  display: flex;
  flex-wrap: wrap;
  gap: 0.8rem;
  margin-top: 0.45rem;
}

.past-info span {
  display: inline-flex;
  align-items: center;
  gap: 0.3rem;
  color: var(--text3);
  font-size: 0.65rem;
}

.past-link {
  display: grid;
  place-items: center;
  width: 36px;
  height: 36px;
  color: var(--text2);
  border: 1px solid var(--border);
  border-radius: 50%;
}

.past-link:hover {
  color: var(--accent);
  border-color: var(--border-gold);
}

.empty-results {
  display: flex;
  align-items: center;
  gap: 0.7rem;
  padding: 1rem;
  color: var(--text2);
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad);
  font-size: 0.72rem;
}

.empty-results button {
  margin-left: auto;
  color: var(--accent);
  border: 0;
  background: transparent;
  cursor: pointer;
}

/* ============================================================
   TRANSITION
   ============================================================ */

.past-enter-active,
.past-leave-active {
  transition:
    opacity 0.25s ease,
    transform 0.25s ease;
}

.past-enter-from,
.past-leave-to {
  opacity: 0;
  transform: translateY(-12px);
}

/* ============================================================
   RESPONSIVE
   ============================================================ */

@media (max-width: 950px) {
  .interest-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 760px) {
  .meetups-header {
    padding-top: 6rem;
  }

  .upcoming-grid {
    grid-template-columns: 1fr;
  }

  .suggest-card {
    align-items: flex-start;
    flex-direction: column;
  }

  .section-heading-row {
    align-items: flex-start;
    flex-direction: column;
  }

  .past-card {
    grid-template-columns: 54px minmax(0, 1fr);
  }

  .past-link {
    display: none;
  }
}

@media (max-width: 560px) {
  .meetups-container {
    width: min(100% - 28px, 1180px);
  }

  .header-content h1 {
    font-size: clamp(2.8rem, 15vw, 4.5rem);
  }

  .interest-grid {
    grid-template-columns: 1fr;
  }

  .no-events {
    align-items: flex-start;
    flex-wrap: wrap;
  }

  .text-button {
    margin-left: 2.65rem;
  }

  .event-bottom {
    align-items: flex-start;
    flex-direction: column;
  }

  .past-card {
    grid-template-columns: 1fr;
  }

  .past-date {
    width: 54px;
  }

  .empty-results {
    align-items: flex-start;
    flex-wrap: wrap;
  }

  .empty-results button {
    margin-left: 0;
  }
}
</style>
```

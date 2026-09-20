<template>
  <main class="meetups-page">
    <!-- =========================================================
         INTRO + SEARCH
         ========================================================= -->
    <section class="meetups-intro">
      <div class="container">
        <div class="intro-content">
          <span class="eyebrow">Sundarbans House · Community</span>

          <h1>
            Find your
            <span>people.</span>
          </h1>

          <p>
            Discover people, meetups and communities around you.
            Learn something, build something, or simply meet people
            from the Sundarbans community.
          </p>
        </div>

        <!-- SEARCH -->
        <div class="search-wrapper">
          <Search :size="20" />

          <input
            v-model="searchQuery"
            type="search"
            placeholder="Search anything — city, meetup, venue, topic..."
            aria-label="Search meetups"
            @keyup.escape="clearSearch"
          />

          <span
            v-if="searchQuery"
            class="search-result-count"
          >
            {{ filteredMeetups.length }}
          </span>

          <button
            v-if="searchQuery"
            class="clear-search"
            type="button"
            aria-label="Clear search"
            @click="clearSearch"
          >
            <X :size="17" />
          </button>
        </div>

        <!-- =======================================================
             CITY — DIRECTLY BELOW SEARCH
             ======================================================= -->
        <div class="city-selector">
          <div class="city-selector-header">
            <span>Choose your city</span>

            <button
              v-if="selectedCity"
              type="button"
              @click="selectedCity = ''"
            >
              Clear
            </button>
          </div>

          <div class="city-list">
            <button
              type="button"
              class="city-pill"
              :class="{ active: selectedCity === '' }"
              @click="selectedCity = ''"
            >
              All cities
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
    <section class="intent-section">
      <div class="container">
        <div class="section-heading">
          <span class="eyebrow">Explore</span>

          <h2>What are you looking for?</h2>

          <p>
            Start with what you want to do. We'll show the relevant
            meetups from the community.
          </p>
        </div>

        <div class="intent-grid">
          <button
            v-for="intent in intents"
            :key="intent.key"
            type="button"
            class="intent-card"
            :class="{ active: activeIntent === intent.key }"
            @click="selectIntent(intent.key)"
          >
            <div class="intent-icon">
              <component
                :is="intent.icon"
                :size="21"
                :stroke-width="1.7"
              />
            </div>

            <div class="intent-content">
              <strong>{{ intent.title }}</strong>
              <span>{{ intent.description }}</span>
            </div>

            <ArrowUpRight :size="16" />
          </button>
        </div>
      </div>
    </section>

    <!-- =========================================================
         UPCOMING
         ========================================================= -->
    <section class="upcoming-section">
      <div class="container">
        <div class="section-heading section-heading--row">
          <div>
            <span class="eyebrow">What's next</span>

            <h2>Upcoming meetups</h2>
          </div>

          <span
            v-if="upcomingMeetups.length"
            class="result-count"
          >
            {{ upcomingMeetups.length }}
            {{ upcomingMeetups.length === 1 ? 'event' : 'events' }}
          </span>
        </div>

        <div
          v-if="upcomingMeetups.length"
          class="upcoming-grid"
        >
          <article
            v-for="meetup in upcomingMeetups"
            :key="meetup.key"
            class="upcoming-card"
          >
            <div class="upcoming-card-top">
              <span class="city-label">
                {{ meetup.city }}
              </span>

              <span class="upcoming-label">
                <span></span>
                Upcoming
              </span>
            </div>

            <h3>{{ meetup.title }}</h3>

            <p v-if="meetup.about">
              {{ meetup.about }}
            </p>

            <div class="meetup-meta">
              <span v-if="meetup.date">
                <CalendarDays :size="15" />
                {{ meetup.date }}
              </span>

              <span v-if="meetup.time">
                <Clock3 :size="15" />
                {{ meetup.time }}
              </span>

              <span v-if="meetup.location">
                <MapPin :size="15" />
                {{ meetup.location }}
              </span>
            </div>

            <div class="upcoming-footer">
              <RouterLink
                :to="`/meetups/${meetup.slug}`"
                class="btn btn--primary btn--sm"
              >
                Explore
                <ArrowRight :size="15" />
              </RouterLink>

              <a
                v-if="meetup.externalUrl"
                :href="meetup.externalUrl"
                target="_blank"
                rel="noopener noreferrer"
                class="event-link"
              >
                Event details
                <ExternalLink :size="13" />
              </a>
            </div>
          </article>
        </div>

        <!-- Don't waste space if there are no upcoming events -->
        <div
          v-else
          class="no-upcoming"
        >
          <CalendarDays :size="19" />

          <span>
            No upcoming meetups are published yet.
          </span>

          <button
            type="button"
            @click="showPastMeetups = true"
          >
            Explore past meetups
            <ArrowRight :size="14" />
          </button>
        </div>
      </div>
    </section>

    <!-- =========================================================
         SUGGEST
         ========================================================= -->
    <section class="suggest-section">
      <div class="container">
        <div class="suggest-card">
          <div>
            <span class="eyebrow">
              Make the community bigger
            </span>

            <h2>
              Don't see a meetup
              <span>near you?</span>
            </h2>

            <p>
              Suggest a city, gathering or idea for the community.
            </p>
          </div>

          <a
            href="https://forms.gle/iHeYQsAbsUTBHJJC6"
            target="_blank"
            rel="noopener noreferrer"
            class="btn btn--primary"
          >
            Suggest a meetup
            <ArrowUpRight :size="17" />
          </a>
        </div>
      </div>
    </section>

    <!-- =========================================================
         OPTIONAL PAST MEETUPS
         Hidden by default.
         ========================================================= -->
    <Transition name="expand">
      <section
        v-if="showPastMeetups"
        class="past-section"
      >
        <div class="container">
          <div class="section-heading section-heading--row">
            <div>
              <span class="eyebrow">Community history</span>

              <h2>Past meetups</h2>
            </div>

            <button
              type="button"
              class="close-history"
              @click="showPastMeetups = false"
            >
              Hide
              <X :size="15" />
            </button>
          </div>

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
                <strong>{{ meetup.dateDay }}</strong>
                <span>{{ meetup.dateMonth }}</span>
              </div>

              <div class="past-main">
                <span class="past-city">
                  {{ meetup.city }}
                </span>

                <h3>{{ meetup.title }}</h3>

                <p v-if="meetup.about">
                  {{ meetup.about }}
                </p>

                <div class="past-meta">
                  <span v-if="meetup.location">
                    <MapPin :size="13" />
                    {{ meetup.location }}
                  </span>

                  <span v-if="meetup.attended">
                    <Users :size="13" />
                    {{ meetup.attended }} attended
                  </span>
                </div>
              </div>

              <a
                v-if="meetup.externalUrl"
                :href="meetup.externalUrl"
                target="_blank"
                rel="noopener noreferrer"
                class="past-link"
                aria-label="View meetup"
              >
                <ExternalLink :size="16" />
              </a>
            </article>
          </div>

          <div
            v-else
            class="empty-history"
          >
            <SearchX :size="20" />

            <span>
              No meetups match your current search.
            </span>

            <button
              type="button"
              @click="resetSearch"
            >
              Clear search
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
const activeIntent = ref('');
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
   INTENTS
   ============================================================ */

const intents = [
  {
    key: 'learn',
    title: 'Learn',
    description: 'Study, talks and knowledge sharing',
    icon: BookOpen,
    terms: [
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
    terms: [
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
    description: 'Hangouts, conversations and community',
    icon: Coffee,
    terms: [
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
    description: 'Find your city chapter',
    icon: UsersRound,
    terms: [],
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

function makeDateParts(value) {
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

/*
 * Flexible search:
 *
 * "bangalore"
 * "bang"
 * "tech"
 * "cafe bangalore"
 * "meetup patna"
 *
 * Every individual word only has to appear somewhere in the
 * searchable event information.
 */
function matchesFlexibleSearch(meetup, query) {
  if (!query) return true;

  const searchableText = normalize(
    [
      meetup.title,
      meetup.city,
      meetup.location,
      meetup.about,
      meetup.meetupNumber,
      ...(meetup.tags || []),
    ].join(' ')
  );

  const words = normalize(query)
    .split(' ')
    .filter(Boolean);

  return words.every((word) => {
    return searchableText.includes(word);
  });
}

/* ============================================================
   ALL HISTORICAL MEETUPS
   ============================================================ */

const allMeetups = computed(() => {
  const records = [];

  cities.forEach((city) => {
    const config = regionConfigs[city.key];

    if (!config) return;

    const meetups = Array.isArray(config.pastMeetups)
      ? config.pastMeetups
      : [];

    meetups.forEach((meetup, index) => {
      const date = parseDate(meetup.date);
      const dateParts = makeDateParts(meetup.date);

      records.push({
        ...meetup,

        key:
          meetup.id ??
          `${city.key}-${index}`,

        city: city.name,
        cityKey: city.key,
        slug: city.slug,

        title:
          meetup.title ||
          `${city.name} Community Meetup`,

        about:
          meetup.about ||
          '',

        location:
          meetup.location ||
          meetup.venue ||
          meetup.address1 ||
          '',

        tags: Array.isArray(meetup.tags)
          ? meetup.tags.filter(Boolean)
          : [],

        dateObject: date,

        dateDay: dateParts.day,
        dateMonth: dateParts.month,

        externalUrl:
          meetup.instaUrl ||
          meetup.url ||
          null,
      });
    });
  });

  return records;
});

/* ============================================================
   FLEXIBLE SEARCH + CITY + INTENT
   ============================================================ */

const filteredMeetups = computed(() => {
  let results = [...allMeetups.value];

  /*
   * City filter
   */
  if (selectedCity.value) {
    results = results.filter(
      (meetup) =>
        meetup.cityKey === selectedCity.value
    );
  }

  /*
   * Search
   */
  if (searchQuery.value.trim()) {
    results = results.filter((meetup) =>
      matchesFlexibleSearch(
        meetup,
        searchQuery.value
      )
    );
  }

  /*
   * Intent
   */
  if (activeIntent.value) {
    const intent = intents.find(
      (item) =>
        item.key === activeIntent.value
    );

    if (intent?.terms?.length) {
      results = results.filter((meetup) => {
        const searchableText = normalize(
          [
            meetup.title,
            meetup.about,
            meetup.location,
            ...(meetup.tags || []),
          ].join(' ')
        );

        return intent.terms.some((term) =>
          searchableText.includes(
            normalize(term)
          )
        );
      });
    }
  }

  /*
   * Newest first
   */
  results.sort((a, b) => {
    const aDate =
      a.dateObject?.getTime() ??
      -Infinity;

    const bDate =
      b.dateObject?.getTime() ??
      -Infinity;

    return bDate - aDate;
  });

  return results;
});

/* ============================================================
   UPCOMING
   ============================================================ */

const upcomingMeetups = computed(() => {
  const now = new Date();

  now.setHours(0, 0, 0, 0);

  const results = [];

  cities.forEach((city) => {
    const config = regionConfigs[city.key];

    if (!config?.upcoming) return;

    const event = config.upcoming;

    const date = parseDate(event.date);

    /*
     * Do not show stale upcoming records.
     */
    if (date && date < now) return;

    results.push({
      ...event,

      key: `${city.key}-upcoming`,

      city: city.name,
      cityKey: city.key,
      slug: city.slug,

      title:
        event.title ||
        event.name ||
        `${city.name} Meetup`,

      about:
        event.about ||
        '',

      location:
        event.location ||
        event.venue ||
        event.address1 ||
        '',

      externalUrl:
        event.instaUrl ||
        event.url ||
        null,
    });
  });

  return results.sort((a, b) => {
    const aDate =
      parseDate(a.date)?.getTime() ??
      Infinity;

    const bDate =
      parseDate(b.date)?.getTime() ??
      Infinity;

    return aDate - bDate;
  });
});

/* ============================================================
   ONLY SHOW PAST MEETUPS WHEN USER REQUESTS THEM
   ============================================================ */

const visiblePastMeetups = computed(() => {
  return filteredMeetups.value.slice(0, 12);
});

/* ============================================================
   ACTIONS
   ============================================================ */

function selectCity(city) {
  selectedCity.value =
    selectedCity.value === city
      ? ''
      : city;

  showPastMeetups.value = false;
}

function selectIntent(intent) {
  activeIntent.value =
    activeIntent.value === intent
      ? ''
      : intent;

  /*
   * If the user is looking for something,
   * don't suddenly dump the archive onto the page.
   *
   * Only reveal it if there are matching results.
   */
  if (
    activeIntent.value &&
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

function clearSearch() {
  searchQuery.value = '';
}

function resetSearch() {
  searchQuery.value = '';
  selectedCity.value = '';
  activeIntent.value = '';
}
</script>

<style scoped>
/* ============================================================
   PAGE
   ============================================================ */

.meetups-page {
  min-height: 100vh;
  background: var(--bg);
}

/* ============================================================
   INTRO
   ============================================================ */

.meetups-intro {
  padding: clamp(7rem, 12vw, 9rem) 0 2.5rem;
  border-bottom: 1px solid var(--border);
}

.intro-content {
  max-width: 850px;
}

.eyebrow {
  display: inline-flex;
  color: var(--accent);
  font-size: 0.7rem;
  font-weight: 600;
  letter-spacing: 0.15em;
  text-transform: uppercase;
}

.intro-content h1 {
  margin-top: 0.7rem;
  font-family: var(--font-display);
  font-size: clamp(3rem, 7vw, 6.2rem);
  line-height: 0.95;
  letter-spacing: -0.045em;
}

.intro-content h1 span {
  color: var(--accent);
}

.intro-content p {
  max-width: 650px;
  margin-top: 1.4rem;
  color: var(--text2);
  font-size: 0.95rem;
  line-height: 1.75;
}

/* ============================================================
   SEARCH
   ============================================================ */

.search-wrapper {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  margin-top: 2.3rem;
  min-height: 62px;
  padding: 0 1.15rem;
  color: var(--text2);
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
  box-shadow: var(--shadow-sm);
}

.search-wrapper:focus-within {
  border-color: var(--border-gold);
}

.search-wrapper input {
  width: 100%;
  min-width: 0;
  border: 0;
  outline: 0;
  color: var(--text);
  background: transparent;
  font-family: var(--font-body);
  font-size: 0.9rem;
}

.search-wrapper input::placeholder {
  color: var(--text3);
}

.search-result-count {
  color: var(--accent);
  font-size: 0.72rem;
}

.clear-search {
  display: grid;
  place-items: center;
  width: 30px;
  height: 30px;
  flex: none;
  border: 0;
  border-radius: 50%;
  color: var(--text2);
  background: transparent;
  cursor: pointer;
}

.clear-search:hover {
  color: var(--text);
  background: var(--surface2);
}

/* ============================================================
   CITY SELECTOR
   ============================================================ */

.city-selector {
  margin-top: 1.1rem;
}

.city-selector-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 0.65rem;
}

.city-selector-header > span {
  color: var(--text3);
  font-size: 0.68rem;
  font-weight: 600;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.city-selector-header button {
  padding: 0;
  border: 0;
  color: var(--accent);
  background: transparent;
  font-size: 0.7rem;
  cursor: pointer;
}

.city-list {
  display: flex;
  gap: 0.45rem;
  overflow-x: auto;
  padding-bottom: 0.3rem;
  scrollbar-width: none;
}

.city-list::-webkit-scrollbar {
  display: none;
}

.city-pill {
  flex: none;
  padding: 0.58rem 0.9rem;
  color: var(--text2);
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: 999px;
  font-size: 0.72rem;
  cursor: pointer;
  transition:
    background 0.2s ease,
    color 0.2s ease,
    border-color 0.2s ease;
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
   SECTION
   ============================================================ */

.intent-section,
.upcoming-section,
.suggest-section,
.past-section {
  padding: clamp(4rem, 7vw, 6rem) 0;
}

.intent-section,
.past-section {
  background: var(--bg2);
}

.section-heading {
  margin-bottom: 1.8rem;
}

.section-heading--row {
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
}

.section-heading p {
  max-width: 600px;
  margin-top: 0.6rem;
  color: var(--text2);
  font-size: 0.8rem;
  line-height: 1.6;
}

.result-count {
  color: var(--text3);
  font-size: 0.75rem;
}

/* ============================================================
   INTENTS
   ============================================================ */

.intent-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 0.7rem;
}

.intent-card {
  position: relative;
  display: flex;
  align-items: flex-start;
  gap: 0.8rem;
  min-height: 145px;
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

.intent-card:hover,
.intent-card.active {
  transform: translateY(-2px);
  border-color: var(--border-gold);
  background: var(--surface2);
}

.intent-icon {
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

.intent-content {
  padding-right: 0.7rem;
}

.intent-content strong {
  display: block;
  font-family: var(--font-display);
  font-size: 1rem;
}

.intent-content span {
  display: block;
  margin-top: 0.35rem;
  color: var(--text2);
  font-size: 0.72rem;
  line-height: 1.5;
}

.intent-card > svg:last-child {
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

.upcoming-card {
  padding: 1.4rem;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
  transition:
    transform 0.25s ease,
    border-color 0.25s ease;
}

.upcoming-card:hover {
  transform: translateY(-2px);
  border-color: var(--border-card-hover);
}

.upcoming-card-top {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
}

.city-label {
  color: var(--accent);
  font-size: 0.67rem;
  font-weight: 600;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.upcoming-label {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  color: var(--text3);
  font-size: 0.65rem;
}

.upcoming-label span {
  width: 5px;
  height: 5px;
  border-radius: 50%;
  background: var(--accent);
}

.upcoming-card h3 {
  margin-top: 0.65rem;
  font-family: var(--font-display);
  font-size: 1.4rem;
}

.upcoming-card > p {
  margin-top: 0.55rem;
  color: var(--text2);
  font-size: 0.78rem;
  line-height: 1.6;
}

.meetup-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 0.7rem 1rem;
  margin-top: 1rem;
}

.meetup-meta span {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  color: var(--text2);
  font-size: 0.7rem;
}

.upcoming-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
  margin-top: 1.3rem;
  padding-top: 1rem;
  border-top: 1px solid var(--border);
}

.event-link {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  color: var(--text2);
  font-size: 0.7rem;
  text-decoration: none;
}

.event-link:hover {
  color: var(--accent);
}

/* ============================================================
   NO UPCOMING
   ============================================================ */

.no-upcoming {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  padding: 1rem 1.2rem;
  color: var(--text2);
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad);
}

.no-upcoming > svg {
  color: var(--accent);
  flex: none;
}

.no-upcoming span {
  flex: 1;
  font-size: 0.76rem;
}

.no-upcoming button {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  flex: none;
  padding: 0;
  color: var(--accent);
  border: 0;
  background: transparent;
  font-size: 0.7rem;
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
  font-size: 0.8rem;
}

.suggest-card .btn {
  flex: none;
}

/* ============================================================
   PAST MEETUPS
   ============================================================ */

.close-history {
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
  padding: 0.5rem 0.7rem;
  color: var(--text2);
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad);
  font-size: 0.7rem;
  cursor: pointer;
}

.close-history:hover {
  color: var(--text);
  border-color: var(--border-card-hover);
}

.past-list {
  display: flex;
  flex-direction: column;
  gap: 0.55rem;
}

.past-card {
  display: grid;
  grid-template-columns: 65px minmax(0, 1fr) auto;
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
  min-height: 62px;
  background: var(--bg);
  border: 1px solid var(--border);
  border-radius: var(--rad);
}

.past-date strong {
  font-family: var(--font-display);
  font-size: 1.25rem;
}

.past-date span {
  color: var(--accent);
  font-size: 0.62rem;
  text-transform: uppercase;
}

.past-city {
  color: var(--accent);
  font-size: 0.64rem;
  font-weight: 600;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.past-main h3 {
  margin-top: 0.2rem;
  font-family: var(--font-display);
  font-size: 1rem;
}

.past-main p {
  margin-top: 0.3rem;
  color: var(--text2);
  font-size: 0.72rem;
}

.past-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 0.8rem;
  margin-top: 0.45rem;
}

.past-meta span {
  display: inline-flex;
  align-items: center;
  gap: 0.3rem;
  color: var(--text3);
  font-size: 0.67rem;
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

.empty-history {
  display: flex;
  align-items: center;
  gap: 0.7rem;
  padding: 1rem;
  color: var(--text2);
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad);
  font-size: 0.75rem;
}

.empty-history button {
  margin-left: auto;
  color: var(--accent);
  border: 0;
  background: transparent;
  cursor: pointer;
}

/* ============================================================
   TRANSITION
   ============================================================ */

.expand-enter-active,
.expand-leave-active {
  transition:
    opacity 0.25s ease,
    transform 0.25s ease;
}

.expand-enter-from,
.expand-leave-to {
  opacity: 0;
  transform: translateY(-12px);
}

/* ============================================================
   RESPONSIVE
   ============================================================ */

@media (max-width: 950px) {
  .intent-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 760px) {
  .meetups-intro {
    padding-top: 6rem;
  }

  .upcoming-grid {
    grid-template-columns: 1fr;
  }

  .suggest-card {
    align-items: flex-start;
    flex-direction: column;
  }

  .past-card {
    grid-template-columns: 55px minmax(0, 1fr);
  }

  .past-link {
    display: none;
  }
}

@media (max-width: 560px) {
  .intent-grid {
    grid-template-columns: 1fr;
  }

  .section-heading--row {
    align-items: flex-start;
    flex-direction: column;
  }

  .no-upcoming {
    align-items: flex-start;
    flex-wrap: wrap;
  }

  .no-upcoming button {
    margin-left: 1.7rem;
  }

  .past-card {
    grid-template-columns: 1fr;
  }

  .past-date {
    width: 55px;
  }
}

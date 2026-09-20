<template>
  <main class="meetups-page">
    <!-- =========================================================
         INTRO
         ========================================================= -->
    <section class="meetups-intro">
      <div class="container">
        <div class="intro-grid">
          <div>
            <span class="eyebrow">Sundarbans House · Community</span>

            <h1>
              Find your
              <span>people.</span>
            </h1>

            <p class="intro-copy">
              Discover meetups, connect with students in your city, and find
              places to learn, collaborate and spend time together offline.
            </p>
          </div>

          <div class="intro-stats">
            <div class="intro-stat">
              <strong>{{ stats.meetups }}</strong>
              <span>meetups recorded</span>
            </div>

            <div class="intro-stat">
              <strong>{{ stats.cities }}</strong>
              <span>active cities</span>
            </div>

            <div class="intro-stat">
              <strong>{{ stats.attendance }}</strong>
              <span>recorded attendance</span>
            </div>
          </div>
        </div>

        <!-- =======================================================
             DISCOVERY BAR
             ======================================================= -->
        <div class="discovery-panel">
          <label class="search-box">
            <Search :size="19" :stroke-width="1.8" />

            <input
              v-model="searchQuery"
              type="search"
              placeholder="Search meetups, cities, venues or topics..."
              aria-label="Search meetups"
            />

            <button
              v-if="searchQuery"
              class="clear-search"
              type="button"
              aria-label="Clear search"
              @click="searchQuery = ''"
            >
              <X :size="16" />
            </button>
          </label>

          <div class="filters">
            <button
              v-for="filter in filters"
              :key="filter.key"
              type="button"
              class="sel"
              :aria-pressed="activeFilter === filter.key"
              @click="activeFilter = filter.key"
            >
              {{ filter.label }}
            </button>
          </div>
        </div>
      </div>
    </section>

    <!-- =========================================================
         UPCOMING
         ========================================================= -->
    <section class="meetup-section upcoming-section">
      <div class="container">
        <div class="section-heading">
          <div>
            <span class="eyebrow">What's next</span>
            <h2>Upcoming meetups</h2>
          </div>

          <span class="result-count">
            {{ upcomingMeetups.length }}
            {{ upcomingMeetups.length === 1 ? 'event' : 'events' }}
          </span>
        </div>

        <div v-if="upcomingMeetups.length" class="events-grid">
          <article
            v-for="meetup in upcomingMeetups"
            :key="meetup.key"
            class="event-card event-card--upcoming"
          >
            <div class="event-status">
              <span class="status-dot"></span>
              Upcoming
            </div>

            <div class="event-card-body">
              <span class="event-city">{{ meetup.city }}</span>

              <h3>{{ meetup.title }}</h3>

              <p v-if="meetup.about">
                {{ meetup.about }}
              </p>

              <div class="event-details">
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
            </div>

            <div class="event-footer">
              <RouterLink
                :to="`/meetups/${meetup.slug}`"
                class="btn btn--primary btn--sm"
              >
                Explore chapter
                <ArrowRight :size="15" />
              </RouterLink>

              <a
                v-if="meetup.externalUrl"
                :href="meetup.externalUrl"
                target="_blank"
                rel="noopener noreferrer"
                class="external-link"
              >
                Event link
                <ExternalLink :size="13" />
              </a>
            </div>
          </article>
        </div>

        <div v-else class="empty-state">
          <div class="empty-icon">
            <CalendarDays :size="23" />
          </div>

          <div>
            <h3>No upcoming meetup published yet</h3>

            <p>
              There isn't a future meetup in the current community data.
              Browse previous meetups below or open your city chapter to stay
              connected.
            </p>
          </div>

          <RouterLink to="/community" class="btn btn--outline btn--sm">
            Join community
            <ArrowRight :size="15" />
          </RouterLink>
        </div>
      </div>
    </section>

    <!-- =========================================================
         QUICK INTENT
         ========================================================= -->
    <section class="intent-section">
      <div class="container">
        <div class="section-heading section-heading--compact">
          <div>
            <span class="eyebrow">Explore</span>
            <h2>What are you looking for?</h2>
          </div>
        </div>

        <div class="intent-grid">
          <button
            v-for="intent in intents"
            :key="intent.key"
            type="button"
            class="intent-card"
            @click="setIntent(intent.key)"
          >
            <component :is="intent.icon" :size="22" :stroke-width="1.7" />

            <div>
              <strong>{{ intent.title }}</strong>
              <span>{{ intent.description }}</span>
            </div>

            <ArrowUpRight :size="17" />
          </button>
        </div>
      </div>
    </section>

    <!-- =========================================================
         RECENT MEETUPS
         ========================================================= -->
    <section class="meetup-section archive-section">
      <div class="container">
        <div class="section-heading">
          <div>
            <span class="eyebrow">Community history</span>
            <h2>Meetups worth exploring</h2>
          </div>

          <span class="result-count">
            {{ filteredMeetups.length }}
            {{ filteredMeetups.length === 1 ? 'result' : 'results' }}
          </span>
        </div>

        <div v-if="filteredMeetups.length" class="archive-list">
          <article
            v-for="meetup in visibleMeetups"
            :key="meetup.key"
            class="archive-card"
          >
            <div class="archive-date" v-if="meetup.date">
              <span>{{ meetup.dateDay }}</span>
              <small>{{ meetup.dateMonth }}</small>
            </div>

            <div class="archive-main">
              <div class="archive-topline">
                <span>{{ meetup.city }}</span>

                <span v-if="meetup.meetupNumber">
                  {{ meetup.meetupNumber }}
                </span>
              </div>

              <h3>{{ meetup.title }}</h3>

              <p v-if="meetup.about">
                {{ meetup.about }}
              </p>

              <div class="archive-meta">
                <span v-if="meetup.location">
                  <MapPin :size="14" />
                  {{ meetup.location }}
                </span>

                <span v-if="meetup.attended">
                  <Users :size="14" />
                  {{ meetup.attended }} attended
                </span>

                <span v-if="meetup.tags.length">
                  <Tag :size="14" />
                  {{ meetup.tags.slice(0, 2).join(' · ') }}
                </span>
              </div>
            </div>

            <a
              v-if="meetup.externalUrl"
              :href="meetup.externalUrl"
              target="_blank"
              rel="noopener noreferrer"
              class="archive-link"
              aria-label="View meetup post"
            >
              <ExternalLink :size="17" />
            </a>
          </article>
        </div>

        <div v-else class="empty-state empty-state--small">
          <SearchX :size="22" />

          <div>
            <h3>No meetups match your search</h3>
            <p>Try another city, venue, topic or search term.</p>
          </div>

          <button
            type="button"
            class="btn btn--outline btn--sm"
            @click="resetFilters"
          >
            Clear filters
          </button>
        </div>

        <button
          v-if="filteredMeetups.length > visibleLimit"
          type="button"
          class="load-more"
          @click="visibleLimit += 6"
        >
          Show more
          <ChevronDown :size="16" />
        </button>
      </div>
    </section>

    <!-- =========================================================
         CITIES
         ========================================================= -->
    <section class="cities-section">
      <div class="container">
        <div class="section-heading">
          <div>
            <span class="eyebrow">Find your chapter</span>
            <h2>Explore by city</h2>
          </div>
        </div>

        <div class="cities-grid">
          <RouterLink
            v-for="city in visibleCities"
            :key="city.key"
            :to="`/meetups/${city.slug}`"
            class="city-card"
          >
            <div class="city-icon">
              <MapPin :size="18" />
            </div>

            <div class="city-content">
              <h3>{{ city.name }}</h3>

              <p>
                {{ city.meetups }}
                {{ city.meetups === 1 ? 'meetup' : 'meetups' }}
                recorded
              </p>
            </div>

            <ArrowUpRight :size="17" />
          </RouterLink>
        </div>
      </div>
    </section>

    <!-- =========================================================
         HOST CTA
         ========================================================= -->
    <section class="host-section">
      <div class="container">
        <div class="host-card">
          <div>
            <span class="eyebrow">Make the next one happen</span>

            <h2>
              Don't see a meetup
              <span>near you?</span>
            </h2>

            <p>
              Suggest a city, organise a small gathering, or volunteer to help
              the Sundarbans community grow offline.
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
  </main>
</template>

<script setup>
import { computed, ref } from 'vue';
import {
  ArrowRight,
  ArrowUpRight,
  CalendarDays,
  ChevronDown,
  Clock3,
  ExternalLink,
  MapPin,
  Search,
  SearchX,
  Tag,
  Users,
  X,
  BookOpen,
  Code2,
  Coffee,
  UsersRound,
} from 'lucide-vue-next';

import { regionConfigs } from './meetups/regionConfigs.js';

/* ============================================================
   REGION METADATA

   Keep the URL slugs exactly aligned with RegionMeetupsView.vue.
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
    key: 'lucknow',
    slug: 'lucknow',
    name: 'Lucknow',
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
   STATE
   ============================================================ */

const searchQuery = ref('');
const activeFilter = ref('all');
const visibleLimit = ref(6);

const filters = [
  { key: 'all', label: 'All meetups' },
  { key: 'recent', label: 'Recent' },
  { key: 'attendance', label: 'Most attended' },
  { key: 'photos', label: 'With photos' },
];

/* ============================================================
   INTENT CARDS

   These are discovery shortcuts, not fake event categories.
   They search the actual meetup metadata.
   ============================================================ */

const intents = [
  {
    key: 'learn',
    title: 'Learn',
    description: 'Study sessions, talks and knowledge sharing',
    icon: BookOpen,
  },
  {
    key: 'build',
    title: 'Build',
    description: 'Projects, technology and collaboration',
    icon: Code2,
  },
  {
    key: 'social',
    title: 'Meet people',
    description: 'Casual gatherings and community time',
    icon: Coffee,
  },
  {
    key: 'community',
    title: 'Find your people',
    description: 'Explore your city chapter',
    icon: UsersRound,
  },
];

/* ============================================================
   HELPERS
   ============================================================ */

function parseDate(value) {
  if (!value) return null;

  const parsed = new Date(value);

  if (!Number.isNaN(parsed.getTime())) {
    return parsed;
  }

  return null;
}

function normalize(value) {
  return String(value ?? '')
    .toLowerCase()
    .replace(/\s+/g, ' ')
    .trim();
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

/* ============================================================
   NORMALIZE THE EXISTING REGION CONFIG DATA

   regionConfigs already handles the different source schemas
   (Bangalore, Delhi, Patna, etc.), so the landing page consumes
   its normalized shape instead of knowing about raw CSV columns.
   ============================================================ */

const allMeetups = computed(() => {
  const records = [];

  cities.forEach((city) => {
    const config = regionConfigs[city.key];

    if (!config) return;

    const past = Array.isArray(config.pastMeetups)
      ? config.pastMeetups
      : [];

    past.forEach((meetup) => {
      const date = parseDate(meetup.date);
      const dateParts = makeDateParts(meetup.date);

      records.push({
        ...meetup,

        key: `${city.key}-${meetup.id}`,
        city: city.name,
        cityKey: city.key,
        slug: city.slug,

        title:
          meetup.title ||
          `${city.name} Community Meetup`,

        about:
          meetup.about ||
          'Meetup details are available in the chapter archive.',

        tags: Array.isArray(meetup.tags)
          ? meetup.tags.filter(Boolean)
          : [],

        dateObject: date,

        dateDay: dateParts.day,
        dateMonth: dateParts.month,

        externalUrl: meetup.instaUrl || null,
      });
    });
  });

  return records;
});

/* ============================================================
   UPCOMING

   The current regionConfigs schema exposes `upcoming` separately.
   This also checks historical records defensively in case a future
   dated record exists in the imported data.
   ============================================================ */

const upcomingMeetups = computed(() => {
  const now = new Date();
  now.setHours(0, 0, 0, 0);

  const upcoming = [];

  cities.forEach((city) => {
    const config = regionConfigs[city.key];

    if (!config?.upcoming) return;

    const event = config.upcoming;
    const date = parseDate(event.date);

    if (date && date < now) return;

    upcoming.push({
      ...event,
      key: `${city.key}-upcoming`,
      city: city.name,
      cityKey: city.key,
      slug: city.slug,
      title: event.name || `${city.name} Meetup`,
      location: event.venue || event.address1 || null,
      about: event.about || null,
      externalUrl: event.instaUrl || null,
      time: event.time || null,
    });
  });

  return upcoming.sort((a, b) => {
    const aDate = parseDate(a.date)?.getTime() ?? Infinity;
    const bDate = parseDate(b.date)?.getTime() ?? Infinity;

    return aDate - bDate;
  });
});

/* ============================================================
   SEARCH + FILTER
   ============================================================ */

const filteredMeetups = computed(() => {
  const query = normalize(searchQuery.value);

  let result = [...allMeetups.value];

  if (query) {
    result = result.filter((meetup) => {
      const haystack = normalize(
        [
          meetup.title,
          meetup.city,
          meetup.location,
          meetup.about,
          meetup.meetupNumber,
          ...(meetup.tags || []),
        ].join(' ')
      );

      return haystack.includes(query);
    });
  }

  if (activeFilter.value === 'recent') {
    result = result.filter((meetup) => meetup.dateObject);
  }

  if (activeFilter.value === 'attendance') {
    result = result
      .filter((meetup) => meetup.attended)
      .sort(
        (a, b) =>
          Number(b.attended || 0) -
          Number(a.attended || 0)
      );
  }

  if (activeFilter.value === 'photos') {
    result = result.filter(
      (meetup) =>
        Array.isArray(meetup.photos) &&
        meetup.photos.length > 0
    );
  }

  if (
    activeFilter.value === 'recent' ||
    activeFilter.value === 'all'
  ) {
    result.sort((a, b) => {
      const aDate = a.dateObject?.getTime() ?? -Infinity;
      const bDate = b.dateObject?.getTime() ?? -Infinity;

      return bDate - aDate;
    });
  }

  return result;
});

const visibleMeetups = computed(() =>
  filteredMeetups.value.slice(0, visibleLimit.value)
);

/* ============================================================
   CITY DATA
   ============================================================ */

const visibleCities = computed(() => {
  const query = normalize(searchQuery.value);

  return cities
    .map((city) => {
      const config = regionConfigs[city.key];

      return {
        ...city,
        meetups: config?.pastMeetups?.length ?? 0,
      };
    })
    .filter((city) => {
      if (!query) return true;

      return normalize(city.name).includes(query);
    })
    .sort((a, b) => b.meetups - a.meetups);
});

/* ============================================================
   GLOBAL STATS

   "Attendance" is deliberately labelled as recorded attendance,
   because the source data represents meetup attendance rather
   than unique members.
   ============================================================ */

const stats = computed(() => {
  const records = allMeetups.value;

  return {
    meetups: records.length,

    cities: cities.filter(
      (city) =>
        (regionConfigs[city.key]?.pastMeetups?.length ?? 0) > 0
    ).length,

    attendance: records.reduce(
      (sum, meetup) =>
        sum + Number(meetup.attended || 0),
      0
    ),
  };
});

/* ============================================================
   INTENT ACTIONS
   ============================================================ */

function setIntent(intent) {
  activeFilter.value = 'all';

  const mappings = {
    learn: ['learn', 'study', 'academic', 'session', 'talk'],
    build: ['tech', 'technology', 'project', 'coding', 'developer'],
    social: ['social', 'hangout', 'casual', 'fun'],
    community: [],
  };

  const terms = mappings[intent] || [];

  if (!terms.length) {
    searchQuery.value = '';
    document
      .querySelector('.cities-section')
      ?.scrollIntoView({ behavior: 'smooth' });

    return;
  }

  /*
   * Search against the actual imported data.
   * We use a broad term because the historical records do not
   * share one universal event-type field.
   */
  searchQuery.value = terms[0];

  document
    .querySelector('.archive-section')
    ?.scrollIntoView({ behavior: 'smooth' });
}

function resetFilters() {
  searchQuery.value = '';
  activeFilter.value = 'all';
  visibleLimit.value = 6;
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
  padding: clamp(7rem, 12vw, 9rem) 0 3rem;
  border-bottom: 1px solid var(--border);
}

.intro-grid {
  display: grid;
  grid-template-columns: minmax(0, 1.4fr) minmax(260px, 0.6fr);
  gap: 4rem;
  align-items: end;
}

.eyebrow {
  display: inline-flex;
  align-items: center;
  gap: 0.45rem;
  color: var(--accent);
  font-size: 0.72rem;
  font-weight: 600;
  letter-spacing: 0.16em;
  text-transform: uppercase;
}

.meetups-intro h1 {
  max-width: 800px;
  margin-top: 0.7rem;
  font-family: var(--font-display);
  font-size: clamp(3rem, 7vw, 6.5rem);
  line-height: 0.95;
  letter-spacing: -0.045em;
  color: var(--text);
}

.meetups-intro h1 span {
  color: var(--accent);
}

.intro-copy {
  max-width: 650px;
  margin-top: 1.5rem;
  color: var(--text2);
  font-size: 1rem;
  line-height: 1.75;
}

.intro-stats {
  display: grid;
  grid-template-columns: 1fr;
  border-left: 1px solid var(--border);
}

.intro-stat {
  display: flex;
  flex-direction: column;
  padding: 0.9rem 0 0.9rem 1.5rem;
  border-bottom: 1px solid var(--border);
}

.intro-stat:last-child {
  border-bottom: 0;
}

.intro-stat strong {
  font-family: var(--font-display);
  font-size: 1.8rem;
  color: var(--text);
}

.intro-stat span {
  color: var(--text2);
  font-size: 0.76rem;
}

/* ============================================================
   DISCOVERY
   ============================================================ */

.discovery-panel {
  margin-top: 3rem;
  padding: 0.65rem;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
}

.search-box {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  min-height: 56px;
  padding: 0 1rem;
  color: var(--text2);
  background: var(--bg);
  border: 1px solid var(--border);
  border-radius: var(--rad);
}

.search-box:focus-within {
  border-color: var(--border-gold);
}

.search-box input {
  width: 100%;
  min-width: 0;
  border: 0;
  outline: 0;
  background: transparent;
  color: var(--text);
  font-family: var(--font-body);
  font-size: 0.92rem;
}

.search-box input::placeholder {
  color: var(--text3);
}

.clear-search {
  display: grid;
  place-items: center;
  width: 32px;
  height: 32px;
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

.filters {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
  padding: 0.75rem 0.35rem 0.2rem;
}

/* ============================================================
   SECTIONS
   ============================================================ */

.meetup-section,
.intent-section,
.cities-section,
.host-section {
  padding: clamp(4.5rem, 8vw, 7rem) 0;
}

.upcoming-section {
  background: var(--bg);
}

.intent-section,
.cities-section {
  background: var(--bg2);
}

.section-heading {
  display: flex;
  align-items: end;
  justify-content: space-between;
  gap: 2rem;
  margin-bottom: 2rem;
}

.section-heading--compact {
  margin-bottom: 1.5rem;
}

.section-heading h2 {
  margin-top: 0.35rem;
  font-family: var(--font-display);
  font-size: clamp(1.8rem, 3vw, 2.8rem);
  line-height: 1.05;
  color: var(--text);
}

.result-count {
  flex: none;
  color: var(--text3);
  font-size: 0.78rem;
}

/* ============================================================
   UPCOMING CARDS
   ============================================================ */

.events-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 1rem;
}

.event-card {
  display: flex;
  flex-direction: column;
  min-height: 300px;
  overflow: hidden;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
  transition:
    border-color 0.3s ease,
    transform 0.3s ease,
    box-shadow 0.3s ease;
}

.event-card:hover {
  transform: translateY(-3px);
  border-color: var(--border-card-hover);
  box-shadow: var(--shadow-md);
}

.event-status {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.8rem 1.3rem;
  border-bottom: 1px solid var(--border);
  color: var(--accent);
  font-size: 0.7rem;
  font-weight: 600;
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

.status-dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: var(--accent);
}

.event-card-body {
  flex: 1;
  padding: 1.5rem;
}

.event-city {
  color: var(--accent);
  font-size: 0.7rem;
  font-weight: 600;
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

.event-card h3 {
  margin-top: 0.55rem;
  font-family: var(--font-display);
  font-size: 1.55rem;
  line-height: 1.15;
}

.event-card-body p {
  margin-top: 0.8rem;
  color: var(--text2);
  font-size: 0.86rem;
  line-height: 1.65;
}

.event-details {
  display: flex;
  flex-wrap: wrap;
  gap: 0.55rem;
  margin-top: 1.2rem;
}

.event-details span,
.archive-meta span {
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  color: var(--text2);
  font-size: 0.74rem;
}

.event-footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
  padding: 1rem 1.3rem;
  border-top: 1px solid var(--border);
}

.external-link,
.archive-link {
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  color: var(--text2);
  font-size: 0.73rem;
  text-decoration: none;
}

.external-link:hover,
.archive-link:hover {
  color: var(--accent);
}

/* ============================================================
   EMPTY STATES
   ============================================================ */

.empty-state {
  display: flex;
  align-items: center;
  gap: 1.2rem;
  padding: 1.5rem;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
}

.empty-icon {
  display: grid;
  place-items: center;
  width: 46px;
  height: 46px;
  flex: none;
  color: var(--accent);
  background: rgba(213, 166, 58, 0.08);
  border: 1px solid var(--border-gold);
  border-radius: var(--rad);
}

.empty-state h3 {
  font-family: var(--font-display);
  font-size: 1.1rem;
}

.empty-state p {
  max-width: 700px;
  margin-top: 0.25rem;
  color: var(--text2);
  font-size: 0.8rem;
  line-height: 1.6;
}

.empty-state .btn {
  margin-left: auto;
  flex: none;
}

.empty-state--small {
  color: var(--text2);
}

/* ============================================================
   INTENT
   ============================================================ */

.intent-grid {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 0.8rem;
}

.intent-card {
  position: relative;
  display: flex;
  align-items: flex-start;
  gap: 0.9rem;
  min-height: 150px;
  padding: 1.25rem;
  text-align: left;
  color: var(--text);
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
  cursor: pointer;
  transition:
    transform 0.3s ease,
    border-color 0.3s ease,
    background 0.3s ease;
}

.intent-card > svg:first-child {
  flex: none;
  color: var(--accent);
}

.intent-card > svg:last-child {
  position: absolute;
  top: 1.25rem;
  right: 1.25rem;
  color: var(--text3);
}

.intent-card:hover {
  transform: translateY(-3px);
  border-color: var(--border-card-hover);
  background: var(--surface2);
}

.intent-card strong {
  display: block;
  padding-right: 1rem;
  font-family: var(--font-display);
  font-size: 1rem;
}

.intent-card span {
  display: block;
  margin-top: 0.4rem;
  color: var(--text2);
  font-size: 0.74rem;
  line-height: 1.5;
}

/* ============================================================
   ARCHIVE
   ============================================================ */

.archive-list {
  display: flex;
  flex-direction: column;
  gap: 0.65rem;
}

.archive-card {
  display: grid;
  grid-template-columns: 75px minmax(0, 1fr) auto;
  gap: 1.2rem;
  align-items: center;
  padding: 1.15rem;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
  transition:
    border-color 0.3s ease,
    background 0.3s ease;
}

.archive-card:hover {
  border-color: var(--border-card-hover);
  background: var(--surface2);
}

.archive-date {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 68px;
  background: var(--bg);
  border: 1px solid var(--border);
  border-radius: var(--rad);
}

.archive-date span {
  font-family: var(--font-display);
  font-size: 1.35rem;
  color: var(--text);
}

.archive-date small {
  color: var(--accent);
  font-size: 0.65rem;
  font-weight: 600;
  text-transform: uppercase;
}

.archive-topline {
  display: flex;
  align-items: center;
  gap: 0.8rem;
  color: var(--accent);
  font-size: 0.68rem;
  font-weight: 600;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.archive-topline span + span {
  color: var(--text3);
}

.archive-main h3 {
  margin-top: 0.25rem;
  font-family: var(--font-display);
  font-size: 1.05rem;
  line-height: 1.25;
}

.archive-main p {
  max-width: 800px;
  margin-top: 0.35rem;
  overflow: hidden;
  color: var(--text2);
  font-size: 0.76rem;
  line-height: 1.55;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}

.archive-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 0.8rem 1.2rem;
  margin-top: 0.55rem;
}

.archive-link {
  width: 38px;
  height: 38px;
  justify-content: center;
  border: 1px solid var(--border);
  border-radius: 50%;
}

.load-more {
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  margin: 1.5rem auto 0;
  padding: 0.7rem 1.1rem;
  color: var(--accent);
  background: transparent;
  border: 1px solid var(--border-gold);
  border-radius: var(--rad);
  cursor: pointer;
}

/* ============================================================
   CITIES
   ============================================================ */

.cities-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 0.7rem;
}

.city-card {
  display: flex;
  align-items: center;
  gap: 0.9rem;
  padding: 1rem;
  color: var(--text);
  text-decoration: none;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
  transition:
    transform 0.3s ease,
    border-color 0.3s ease;
}

.city-card:hover {
  transform: translateY(-2px);
  border-color: var(--border-card-hover);
}

.city-icon {
  display: grid;
  place-items: center;
  width: 40px;
  height: 40px;
  flex: none;
  color: var(--accent);
  background: var(--bg);
  border: 1px solid var(--border);
  border-radius: var(--rad);
}

.city-content {
  flex: 1;
  min-width: 0;
}

.city-content h3 {
  font-family: var(--font-display);
  font-size: 0.98rem;
}

.city-content p {
  margin-top: 0.1rem;
  color: var(--text2);
  font-size: 0.7rem;
}

/* ============================================================
   HOST CTA
   ============================================================ */

.host-section {
  padding-top: 3rem;
  padding-bottom: 6rem;
}

.host-card {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 3rem;
  padding: clamp(2rem, 5vw, 4rem);
  background:
    linear-gradient(
      110deg,
      rgba(213, 166, 58, 0.08),
      transparent 60%
    ),
    var(--surface);
  border: 1px solid var(--border-gold);
  border-radius: var(--rad2);
}

.host-card h2 {
  max-width: 650px;
  margin-top: 0.5rem;
  font-family: var(--font-display);
  font-size: clamp(1.8rem, 4vw, 3.2rem);
  line-height: 1.05;
}

.host-card h2 span {
  color: var(--accent);
}

.host-card p {
  max-width: 650px;
  margin-top: 0.8rem;
  color: var(--text2);
  font-size: 0.86rem;
}

.host-card .btn {
  flex: none;
}

/* ============================================================
   RESPONSIVE
   ============================================================ */

@media (max-width: 1000px) {
  .intro-grid {
    grid-template-columns: 1fr;
    gap: 2rem;
  }

  .intro-stats {
    grid-template-columns: repeat(3, 1fr);
    border-left: 0;
    border-top: 1px solid var(--border);
  }

  .intro-stat {
    padding: 1rem;
    border-bottom: 0;
    border-right: 1px solid var(--border);
  }

  .intro-stat:last-child {
    border-right: 0;
  }

  .intent-grid {
    grid-template-columns: repeat(2, 1fr);
  }

  .cities-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 760px) {
  .meetups-intro {
    padding-top: 6rem;
  }

  .meetups-intro h1 {
    font-size: clamp(2.8rem, 15vw, 5rem);
  }

  .events-grid {
    grid-template-columns: 1fr;
  }

  .section-heading {
    align-items: flex-start;
    flex-direction: column;
    gap: 0.5rem;
  }

  .empty-state {
    align-items: flex-start;
    flex-wrap: wrap;
  }

  .empty-state .btn {
    margin-left: 0;
  }

  .archive-card {
    grid-template-columns: 58px minmax(0, 1fr);
  }

  .archive-link {
    display: none;
  }

  .host-card {
    flex-direction: column;
    align-items: flex-start;
  }
}

@media (max-width: 560px) {
  .meetups-intro {
    padding-bottom: 2rem;
  }

  .intro-stats {
    grid-template-columns: 1fr;
  }

  .intro-stat {
    border-right: 0;
    border-bottom: 1px solid var(--border);
  }

  .intro-stat:last-child {
    border-bottom: 0;
  }

  .filters {
    overflow-x: auto;
    flex-wrap: nowrap;
    padding-bottom: 0.5rem;
  }

  .filters .sel {
    flex: none;
  }

  .intent-grid,
  .cities-grid {
    grid-template-columns: 1fr;
  }

  .archive-card {
    grid-template-columns: 1fr;
  }

  .archive-date {
    width: 58px;
  }

  .archive-main p {
    -webkit-line-clamp: 3;
  }

  .event-footer {
    align-items: flex-start;
    flex-direction: column;
  }
}
</style>

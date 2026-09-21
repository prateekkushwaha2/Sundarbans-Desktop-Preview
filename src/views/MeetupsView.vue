<template>
  <main class="meetups-page">
    <!-- =========================================================
         PLAYFUL EVENT HERO
         ========================================================= -->
    <section
      ref="heroRef"
      class="event-hero"
      @mousemove="handleHeroMove"
      @mouseleave="resetHero"
    >
      <div class="hero-grid"></div>
      <div class="hero-sun"></div>
      <div class="hero-cloud cloud-one"></div>
      <div class="hero-cloud cloud-two"></div>

      <div class="container hero-inner">
        <div class="hero-copy">
          <div class="eyebrow">
            <span class="live-dot"></span>
            Sundarbans Community Meetups
          </div>

          <h1>
            Don't just
            <span class="scribble-word">scroll.</span>
            <br />
            <span class="hero-highlight">belong.</span>
          </h1>

          <p>
            Find your people, discover what your city is doing, and turn an online
            connection into an actual meetup.
          </p>

          <div class="hero-actions">
            <button class="hero-button primary" type="button" @click="focusSearch">
              <Search :size="18" />
              Find a meetup
            </button>

            <a
              class="hero-button secondary"
              href="https://forms.gle/iHeYQsAbsUTBHJJC6"
              target="_blank"
              rel="noopener noreferrer"
            >
              <Plus :size="18" />
              Suggest a meetup
            </a>
          </div>

          <div class="hero-note">
            <span>⌁</span>
            Search a city, topic, venue, meetup number or anything you remember.
          </div>
        </div>

        <!-- CSS-only 3D event object -->
        <div class="hero-stage" aria-hidden="true">
          <div
            class="event-ticket"
            :style="heroTransform"
          >
            <div class="ticket-hole hole-one"></div>
            <div class="ticket-hole hole-two"></div>

            <div class="ticket-top">
              <span class="ticket-label">MEETUPS</span>
              <span class="ticket-mark">✦</span>
            </div>

            <div class="ticket-title">
              MEET
              <br />
              YOUR
              <br />
              PEOPLE.
            </div>

            <div class="ticket-meta">
              <span>PEOPLE</span>
              <span>IDEAS</span>
              <span>CHAOS</span>
            </div>

            <div class="ticket-barcode">
              <i></i><i></i><i></i><i></i><i></i><i></i><i></i>
              <i></i><i></i><i></i><i></i><i></i>
            </div>
          </div>

          <div class="floating-doodle doodle-smile">⌣</div>
          <div class="floating-doodle doodle-star">✦</div>
          <div class="floating-doodle doodle-arrow">↗</div>
          <div class="floating-label label-one">YOUR<br />PEOPLE</div>
          <div class="floating-label label-two">REAL<br />MEETUPS</div>
        </div>
      </div>

      <div class="hero-bottom">
        <div class="container hero-bottom-inner">
          <span>SCROLL TO EXPLORE</span>
          <span class="scroll-line"></span>
          <span>{{ regions.length }} cities</span>
        </div>
      </div>
    </section>

    <!-- =========================================================
         SEARCH
         ========================================================= -->
    <section ref="searchSection" class="search-section">
      <div class="container">
        <div class="search-wrap">
          <div class="search-icon">
            <Search :size="22" />
          </div>

          <input
            v-model="searchQuery"
            type="search"
            placeholder="What are you looking for?"
            autocomplete="off"
            @keydown.esc="searchQuery = ''"
          />

          <div v-if="searchQuery" class="search-count">
            {{ searchResults.length }} found
          </div>

          <button
            v-if="searchQuery"
            class="icon-button"
            type="button"
            aria-label="Clear search"
            @click="searchQuery = ''"
          >
            <X :size="18" />
          </button>
        </div>

        <div class="search-examples">
          <span>Try</span>
          <button type="button" @click="setSearch('Bangalore')">Bangalore</button>
          <button type="button" @click="setSearch('tech')">tech</button>
          <button type="button" @click="setSearch('cafe')">cafe</button>
          <button type="button" @click="setSearch('coding')">coding</button>
        </div>

        <!-- RESULTS ALWAYS APPEAR DIRECTLY BELOW SEARCH -->
        <div v-if="searchQuery.trim()" class="result-drawer">
          <div class="drawer-heading">
            <div>
              <span class="mini-label">SEARCHING THE COMMUNITY</span>
              <h2>
                {{ searchResults.length }}
                {{ searchResults.length === 1 ? 'match' : 'matches' }}
              </h2>
            </div>

            <span class="query-pill">{{ searchQuery }}</span>
          </div>

          <MeetupResults
            :items="searchResults"
            empty-title="Nothing matched that search"
            empty-text="Try another city, topic, venue or keyword."
          />
        </div>
      </div>
    </section>

    <!-- =========================================================
         CITIES
         ========================================================= -->
    <section class="section cities-section">
      <div class="container">
        <div class="section-heading">
          <div>
            <span class="mini-label">WHERE THE COMMUNITY LIVES</span>
            <h2>Find your <span>city.</span></h2>
          </div>

          <p>
            Open a chapter and see what your local community is getting up to.
          </p>
        </div>

        <div class="city-mosaic">
          <button
            v-for="(region, index) in filteredRegions"
            :key="region.slug"
            type="button"
            class="city-card"
            :class="{
              'city-large': index === 0,
              'city-tall': index === 3
            }"
            @click="goToRegion(region.slug)"
          >
            <img :src="region.image" :alt="region.name" loading="lazy" decoding="async" width="1000" height="700" />

            <div class="city-shade"></div>

            <span v-if="region.badge" class="city-badge">
              {{ region.badge }}
            </span>

            <div class="city-doodle">✦</div>

            <div class="city-info">
              <span>{{ region.members }} members</span>
              <strong>{{ region.name }}</strong>
              <small>Explore chapter →</small>
            </div>
          </button>
        </div>

        <div v-if="!filteredRegions.length" class="simple-empty">
          <MapPin :size="26" />
          <strong>No city matches "{{ searchQuery }}"</strong>
          <span>Try a different search.</span>
        </div>
      </div>
    </section>

    <!-- =========================================================
         INTERESTS
         ========================================================= -->
    <section class="section interests-section">
      <div class="container">
        <div class="section-heading centered">
          <span class="mini-label">FIND YOUR KIND OF MEETUP</span>
          <h2>What are you looking <span>for?</span></h2>
          <p>Choose a vibe and we’ll show the meetups that fit it.</p>
        </div>

        <div class="interest-row">
          <button
            v-for="interest in interests"
            :key="interest.key"
            type="button"
            class="interest-card"
            :class="{ selected: activeInterest === interest.key }"
            @click="toggleInterest(interest.key)"
          >
            <span class="interest-number">{{ interest.number }}</span>
            <div class="interest-icon">
              <component :is="interest.icon" :size="27" :stroke-width="1.8" />
            </div>
            <strong>{{ interest.label }}</strong>
            <p>{{ interest.description }}</p>
            <span class="interest-arrow">
              {{ activeInterest === interest.key ? '×' : '↗' }}
            </span>
          </button>
        </div>

        <!-- INTEREST RESULTS DIRECTLY UNDER THE BUTTONS -->
        <div v-if="activeInterest" class="result-drawer interest-drawer">
          <div class="drawer-heading">
            <div>
              <span class="mini-label">MEETUPS FOR YOU</span>
              <h2>{{ activeInterestLabel }}</h2>
            </div>

            <div class="interest-result-tools">
              <span>{{ interestResults.length }} {{ interestResults.length === 1 ? 'meetup' : 'meetups' }}</span>
              <button
                type="button"
                class="text-button"
                @click="activeInterest = null"
              >
                Clear ×
              </button>
            </div>
          </div>

          <MeetupResults
            :items="interestResults"
            empty-title="No exact matches yet"
            empty-text="There aren’t enough tagged meetups here yet. Be the person who starts one."
          />
        </div>
      </div>
    </section>

    <!-- =========================================================
         UPCOMING EVENTS
         ========================================================= -->
    <section class="section upcoming-section">
      <div class="container">
        <div class="section-heading">
          <div>
            <span class="mini-label">DON'T MISS THIS</span>
            <h2>Meetups <span>coming up.</span></h2>
          </div>

          <p>
            Only currently scheduled meetups live here. The archive stays out of
            your way until you ask for it.
          </p>
        </div>

        <div v-if="upcomingMeetups.length" class="event-board">
          <article
            v-for="(event, index) in upcomingMeetups"
            :key="event.key"
            class="event-card"
            :class="{ 'event-featured': index === 0 }"
          >
            <div class="event-date">
              <span>{{ event.month }}</span>
              <strong>{{ event.day }}</strong>
            </div>

            <div class="event-main">
              <div class="event-topline">
                <span>{{ event.city }}</span>
                <span v-if="event.meetupNumber">{{ event.meetupNumber }}</span>
              </div>

              <h3>{{ event.title }}</h3>

              <p>{{ event.about }}</p>

              <div class="event-meta">
                <span v-if="event.time">
                  <Clock :size="15" />
                  {{ event.time }}
                </span>
                <span v-if="event.location">
                  <MapPin :size="15" />
                  {{ event.location }}
                </span>
              </div>
            </div>

            <a
              v-if="event.mapsUrl"
              :href="event.mapsUrl"
              target="_blank"
              rel="noopener noreferrer"
              class="event-go"
            >
              Go →
            </a>
          </article>
        </div>

        <div v-else class="empty-board">
          <div class="empty-face">:)</div>
          <h3>Nothing scheduled yet.</h3>
          <p>That means you have an excellent opportunity to suggest something.</p>
          <a
            class="dark-button"
            href="https://forms.gle/iHeYQsAbsUTBHJJC6"
            target="_blank"
            rel="noopener noreferrer"
          >
            Suggest a meetup →
          </a>
        </div>
      </div>
    </section>

    <!-- =========================================================
         SUGGEST A MEETUP
         ========================================================= -->
    <section class="section suggestion-section">
      <div class="container">
        <div class="suggest-card">
          <div class="suggest-art" aria-hidden="true">
            <div class="speech speech-one">HEY!</div>
            <div class="speech speech-two">LET'S<br />MEET.</div>
            <div class="suggest-face">
              <span></span>
              <span></span>
              <i></i>
            </div>
          </div>

          <div class="suggest-copy">
            <span class="mini-label">START SOMETHING</span>
            <h2>Got an idea?<br /><span>Start a meetup.</span></h2>
            <p>
              A coding session, chai meetup, study circle, game night, design jam — anything.
              Suggest it and send the idea to the community team.
            </p>

            <a
              class="dark-button"
              href="https://forms.gle/iHeYQsAbsUTBHJJC6"
              target="_blank"
              rel="noopener noreferrer"
            >
              Suggest a meetup →
            </a>
          </div>
        </div>

      </div>
    </section>

    <!-- =========================================================
         PAST EVENTS
         ========================================================= -->
    <section class="archive-section">
      <div class="container">
        <button
          type="button"
          class="archive-trigger"
          @click="showPast = !showPast"
        >
          <span>
            <Archive :size="19" />
            {{ showPast ? 'Hide past meetups' : 'Explore past meetups' }}
          </span>
          <strong>{{ showPast ? '−' : '+' }}</strong>
        </button>

        <div v-if="showPast" class="result-drawer archive-drawer">
          <div class="drawer-heading">
            <div>
              <span class="mini-label">THE ARCHIVE</span>
              <h2>Meetups we've already shared.</h2>
            </div>
            <span>{{ filteredPast.length }} meetups</span>
          </div>

          <div v-if="filteredPast.length" class="past-board">
            <article
              v-for="item in filteredPast"
              :key="item.key"
              class="past-card"
            >
              <div class="past-date">
                <span>{{ item.month || 'PAST' }}</span>
                <strong>{{ item.day || '—' }}</strong>
              </div>

              <div class="past-main">
                <div class="past-topline">
                  <span>{{ item.city }}</span>
                  <span v-if="item.meetupNumber">{{ item.meetupNumber }}</span>
                </div>

                <h3>{{ item.title }}</h3>

                <p>{{ item.about }}</p>

                <div class="past-meta">
                  <span v-if="item.location">
                    <MapPin :size="14" />
                    {{ item.location }}
                  </span>
                  <span v-if="item.tags?.length">
                    {{ item.tags.slice(0, 3).join(' · ') }}
                  </span>
                </div>
              </div>

              <a
                v-if="item.instaUrl"
                :href="item.instaUrl"
                target="_blank"
                rel="noopener noreferrer"
                class="past-link"
              >
                Details →
              </a>
            </article>
          </div>

          <div v-else class="result-empty">
            <div class="empty-star">✦</div>
            <h3>No archived meetups found</h3>
            <p>Try clearing your search.</p>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<script setup>
import { computed, defineComponent, h, onBeforeUnmount, onMounted, ref } from 'vue';
import { useRouter } from 'vue-router';
import {
  Archive,
  CalendarDays,
  Clock,
  Code2,
  Globe2,
  Lightbulb,
  MapPin,
  Plus,
  Search,
  Sparkles,
  Users,
  X,
} from 'lucide-vue-next';

import { regionConfigs } from './meetups/regionConfigs.js';

const router = useRouter();

const heroRef = ref(null);
const searchSection = ref(null);

const searchQuery = ref('');
const activeInterest = ref(null);
const showPast = ref(false);

const heroX = ref(0);
const heroY = ref(0);

const imageBase = 'https://res.cloudinary.com/l59gy0g2/image/upload/f_auto,q_auto:good,w_1000,c_limit';

const regions = [
  {
    slug: 'delhi-ncr',
    configSlug: 'delhi',
    name: 'Delhi-NCR',
    members: '320+',
    badge: 'Most Active',
    image: `${imageBase}/v1785911362/sundarbans/src/assets/regions/delhi.jpg`,
  },
  {
    slug: 'mumbai',
    configSlug: 'mumbai',
    name: 'Mumbai',
    members: '450+',
    badge: 'Largest Chapter',
    image: `${imageBase}/v1785911367/sundarbans/src/assets/regions/mumbai.jpg`,
  },
  {
    slug: 'bangalore',
    configSlug: 'bangalore',
    name: 'Bangalore',
    members: '390+',
    badge: '',
    image: `${imageBase}/v1785911358/sundarbans/src/assets/regions/bangalore.jpg`,
  },
  {
    slug: 'kolkata',
    configSlug: 'kolkata',
    name: 'Kolkata',
    members: '280+',
    badge: '',
    image: `${imageBase}/v1785911364/sundarbans/src/assets/regions/kolkata.jpg`,
  },
  {
    slug: 'hyderabad',
    configSlug: 'hyderabad',
    name: 'Hyderabad',
    members: '210+',
    badge: '',
    image: `${imageBase}/v1785911363/sundarbans/src/assets/regions/hyderabad.jpg`,
  },
  {
    slug: 'patna',
    configSlug: 'patna',
    name: 'Patna',
    members: '180+',
    badge: '',
    image: `${imageBase}/v1785911369/sundarbans/src/assets/regions/patna.jpg`,
  },
  {
    slug: 'chandigarh',
    configSlug: 'chandigarh',
    name: 'Chandigarh',
    members: '120+',
    badge: 'Rising',
    image: `${imageBase}/v1785911359/sundarbans/src/assets/regions/chandigarh.webp`,
  },
  {
    slug: 'chennai',
    configSlug: 'chennai',
    name: 'Chennai',
    members: '150+',
    badge: '',
    image: `${imageBase}/v1785911360/sundarbans/src/assets/regions/chennai.jpg`,
  },
  {
    slug: 'lucknow',
    configSlug: 'lucknow',
    name: 'Lucknow',
    members: '110+',
    badge: '',
    image: `${imageBase}/v1785911366/sundarbans/src/assets/regions/lucknow.jpg`,
  },
];

const interests = [
  {
    key: 'learn',
    number: '01',
    label: 'Learn',
    description: 'Study sessions, workshops, talks and knowledge sharing.',
    icon: Lightbulb,
  },
  {
    key: 'build',
    number: '02',
    label: 'Build',
    description: 'Coding, projects, startups, AI and making things.',
    icon: Code2,
  },
  {
    key: 'people',
    number: '03',
    label: 'Meet People',
    description: 'Friends, networking, chai, conversations and connections.',
    icon: Users,
  },
  {
    key: 'community',
    number: '04',
    label: 'Community',
    description: 'Chapter events, social gatherings and shared experiences.',
    icon: Globe2,
  },
];

const interestKeywords = {
  learn: [
    'learn', 'learning', 'study', 'education', 'academic', 'discussion',
    'workshop', 'session', 'knowledge', 'exam', 'doubt', 'talk', 'lecture',
  ],
  build: [
    'build', 'building', 'project', 'coding', 'code', 'developer',
    'development', 'technology', 'tech', 'ai', 'data', 'startup', 'hack',
    'product', 'programming', 'design',
  ],
  people: [
    'network', 'networking', 'social', 'connect', 'connection', 'friends',
    'meet', 'people', 'cafe', 'chai', 'hangout', 'gathering',
  ],
  community: [
    'community', 'chapter', 'student', 'society', 'house', 'sundarbans',
    'iitm', 'iit madras', 'meetup', 'event', 'collaboration', 'social',
  ],
};

function firstValue(object, keys, fallback = '') {
  for (const key of keys) {
    const value = object?.[key];
    if (value !== undefined && value !== null && value !== '') return value;
  }
  return fallback;
}

function toArray(value) {
  if (Array.isArray(value)) return value;
  if (typeof value === 'string') {
    return value
      .split(',')
      .map((item) => item.trim())
      .filter(Boolean);
  }
  return [];
}

function parseDateParts(dateValue) {
  if (!dateValue) {
    return { day: '', month: '' };
  }

  const date = new Date(dateValue);

  if (Number.isNaN(date.getTime())) {
    return { day: '', month: '' };
  }

  return {
    day: String(date.getDate()).padStart(2, '0'),
    month: date.toLocaleString('en-IN', { month: 'short' }).toUpperCase(),
  };
}

function normalizeMeetup(raw, city, type, key) {
  const title = firstValue(
    raw,
    ['title', 'name', 'eventName', 'meetupName'],
    `${city} Community Meetup`,
  );

  const date = firstValue(raw, ['date', 'eventDate', 'meetupDate']);
  const parts = parseDateParts(date);

  return {
    ...raw,
    key,
    city,
    type,
    title,
    date,
    day: parts.day,
    month: parts.month,
    time: firstValue(raw, ['time', 'eventTime', 'meetupTime']),
    location: firstValue(
      raw,
      ['location', 'venue', 'address1', 'address', 'place'],
    ),
    about: firstValue(
      raw,
      ['about', 'description', 'desc', 'details'],
      'Community meetup.',
    ),
    meetupNumber: firstValue(
      raw,
      ['meetupNumber', 'number', 'id'],
    ),
    tags: toArray(firstValue(raw, ['tags', 'tag', 'topics'])),
    mapsUrl: firstValue(raw, ['mapsUrl', 'googleMaps', 'mapUrl']),
    instaUrl: firstValue(raw, ['instaUrl', 'instagram', 'instagramUrl']),
  };
}

const allMeetups = computed(() => {
  const result = [];

  Object.entries(regionConfigs || {}).forEach(([cityKey, config]) => {
    const city = firstValue(
      config,
      ['cityName', 'heroTitle', 'name', 'title'],
      cityKey.charAt(0).toUpperCase() + cityKey.slice(1),
    );

    const upcoming = firstValue(config, ['upcoming', 'nextMeetup'], null);

    if (upcoming && typeof upcoming === 'object') {
      result.push(
        normalizeMeetup(
          upcoming,
          city,
          'upcoming',
          `${cityKey}-upcoming`,
        ),
      );
    }

    const past = firstValue(config, ['pastMeetups', 'past', 'events'], []);

    if (Array.isArray(past)) {
      past.forEach((item, index) => {
        result.push(
          normalizeMeetup(
            item,
            city,
            'past',
            `${cityKey}-past-${item?.id || index}`,
          ),
        );
      });
    }
  });

  return result;
});

const upcomingMeetups = computed(() =>
  allMeetups.value.filter((item) => item.type === 'upcoming'),
);

const pastMeetups = computed(() =>
  allMeetups.value.filter((item) => item.type === 'past'),
);

function searchableText(item) {
  return [
    item.city,
    item.title,
    item.location,
    item.about,
    item.date,
    item.time,
    item.meetupNumber,
    ...(item.tags || []),
  ]
    .filter(Boolean)
    .join(' ')
    .toLowerCase();
}

function matchesSearch(item, query) {
  const terms = query
    .toLowerCase()
    .trim()
    .split(/\s+/)
    .filter(Boolean);

  if (!terms.length) return true;

  const text = searchableText(item);
  return terms.every((term) => text.includes(term));
}

const searchResults = computed(() => {
  if (!searchQuery.value.trim()) return [];

  return allMeetups.value
    .filter((item) => matchesSearch(item, searchQuery.value))
    .slice(0, 30);
});

const filteredPast = computed(() => {
  if (!searchQuery.value.trim()) return pastMeetups.value;
  return pastMeetups.value.filter((item) =>
    matchesSearch(item, searchQuery.value),
  );
});

const filteredRegions = computed(() => {
  if (!searchQuery.value.trim()) return regions;

  const terms = searchQuery.value
    .toLowerCase()
    .trim()
    .split(/\s+/)
    .filter(Boolean);

  return regions.filter((region) => {
    const cityMeetups = allMeetups.value.filter(
      (item) =>
        item.city.toLowerCase().includes(region.name.toLowerCase()) ||
        item.city.toLowerCase().includes(region.configSlug),
    );

    const text = [
      region.name,
      region.slug,
      region.members,
      ...cityMeetups.map(searchableText),
    ]
      .join(' ')
      .toLowerCase();

    return terms.every((term) => text.includes(term));
  });
});

const activeInterestLabel = computed(() => {
  return (
    interests.find((item) => item.key === activeInterest.value)?.label || ''
  );
});

const interestResults = computed(() => {
  if (!activeInterest.value) return [];

  const keywords = interestKeywords[activeInterest.value] || [];

  let results = allMeetups.value.filter((item) => {
    const text = searchableText(item);
    return keywords.some((keyword) => text.includes(keyword));
  });

  if (searchQuery.value.trim()) {
    results = results.filter((item) =>
      matchesSearch(item, searchQuery.value),
    );
  }

  return results.slice(0, 30);
});

function toggleInterest(key) {
  activeInterest.value =
    activeInterest.value === key ? null : key;
}

function setSearch(value) {
  searchQuery.value = value;
  activeInterest.value = null;
}

function goToRegion(slug) {
  router.push(`/meetups/${slug}`);
}

function focusSearch() {
  searchSection.value?.scrollIntoView({
    behavior: 'smooth',
    block: 'center',
  });

  requestAnimationFrame(() => {
    const input = searchSection.value?.querySelector('input');
    input?.focus();
  });
}

function handleHeroMove(event) {
  const rect = heroRef.value?.getBoundingClientRect();

  if (!rect) return;

  const x = (event.clientX - rect.left) / rect.width - 0.5;
  const y = (event.clientY - rect.top) / rect.height - 0.5;

  heroX.value = x * 10;
  heroY.value = y * -10;
}

function resetHero() {
  heroX.value = 0;
  heroY.value = 0;
}

const heroTransform = computed(() => ({
  transform: `rotateY(${heroX.value}deg) rotateX(${heroY.value}deg)`,
}));

/*
 * Small local component keeps every result card identical.
 * No extra component file is required.
 */
const MeetupResults = defineComponent({
  name: 'MeetupResults',
  props: {
    items: {
      type: Array,
      default: () => [],
    },
    emptyTitle: {
      type: String,
      default: 'Nothing found',
    },
    emptyText: {
      type: String,
      default: 'Try another search.',
    },
  },
  setup(props) {
    return () =>
      props.items.length
        ? h(
            'div',
            { class: 'result-grid' },
            props.items.map((item) =>
              h(
                'article',
                {
                  key: item.key,
                  class: 'mini-event-card',
                },
                [
                  h(
                    'div',
                    { class: 'mini-event-top' },
                    [
                      h('span', { class: 'mini-event-city' }, item.city),
                      item.type === 'upcoming'
                        ? h(
                            'span',
                            { class: 'upcoming-chip' },
                            'UPCOMING',
                          )
                        : h(
                            'span',
                            { class: 'past-chip' },
                            'PAST',
                          ),
                    ],
                  ),

                  h('h3', item.title),

                  h(
                    'div',
                    { class: 'mini-event-meta' },
                    [
                      item.date
                        ? h(
                            'span',
                            {},
                            [
                              h(CalendarDays, { size: 14 }),
                              item.date,
                            ],
                          )
                        : null,

                      item.location
                        ? h(
                            'span',
                            {},
                            [
                              h(MapPin, { size: 14 }),
                              item.location,
                            ],
                          )
                        : null,
                    ].filter(Boolean),
                  ),

                  h('p', item.about),

                  item.tags?.length
                    ? h(
                        'div',
                        { class: 'mini-tags' },
                        item.tags.slice(0, 4).map((tag) =>
                          h('span', { key: tag }, tag),
                        ),
                      )
                    : null,

                  item.mapsUrl || item.instaUrl
                    ? h(
                        'div',
                        { class: 'mini-event-links' },
                        [
                          item.mapsUrl
                            ? h(
                                'a',
                                {
                                  href: item.mapsUrl,
                                  target: '_blank',
                                  rel: 'noopener noreferrer',
                                },
                                'Location →',
                              )
                            : null,

                          item.instaUrl
                            ? h(
                                'a',
                                {
                                  href: item.instaUrl,
                                  target: '_blank',
                                  rel: 'noopener noreferrer',
                                },
                                'Details →',
                              )
                            : null,
                        ].filter(Boolean),
                      )
                    : null,
                ],
              ),
            ),
          )
        : h(
            'div',
            { class: 'result-empty' },
            [
              h('div', { class: 'empty-star' }, '✦'),
              h('h3', props.emptyTitle),
              h('p', props.emptyText),
            ],
          );
  },
});

let resizeHandler;

onMounted(() => {
  resizeHandler = () => {
    if (window.innerWidth < 700) resetHero();
  };

  window.addEventListener('resize', resizeHandler);
});

onBeforeUnmount(() => {
  window.removeEventListener('resize', resizeHandler);
});
</script>

<style scoped>
/* =========================================================
   BASE
   ========================================================= */

.meetups-page {
  --ink: #171717;
  --paper: #f5f0e6;
  --paper-2: #ebe4d6;
  --yellow: #f6c945;
  --pink: #ff7c91;
  --blue: #71b8ff;
  --green: #9edc79;
  --purple: #9c8cff;
  --muted: #706d66;
  --line: rgba(23, 23, 23, 0.14);
  min-height: 100vh;
  overflow: hidden;
  background: var(--paper);
  color: var(--ink);
}

.meetups-page button,
.meetups-page input,
.meetups-page textarea {
  font: inherit;
}

.container {
  width: min(1180px, calc(100% - 40px));
  margin: 0 auto;
}

.section {
  padding: 105px 0;
}

.mini-label {
  display: inline-block;
  margin-bottom: 10px;
  font-size: 0.68rem;
  font-weight: 900;
  letter-spacing: 0.15em;
  text-transform: uppercase;
}

.section-heading {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  gap: 40px;
  margin-bottom: 40px;
}

.section-heading.centered {
  display: block;
  text-align: center;
}

.section-heading h2 {
  margin: 0;
  font-family: var(--font-display);
  font-size: clamp(2.4rem, 5vw, 4.5rem);
  line-height: 0.95;
  letter-spacing: -0.045em;
}

.section-heading h2 span {
  position: relative;
  display: inline-block;
}

.section-heading h2 span::after {
  position: absolute;
  left: 2%;
  right: -4%;
  bottom: -7px;
  height: 8px;
  content: "";
  border-radius: 50%;
  background: var(--yellow);
  transform: rotate(-2deg);
  z-index: -1;
}

.section-heading p {
  max-width: 360px;
  margin: 0;
  color: var(--muted);
  line-height: 1.65;
}

/* =========================================================
   HERO
   ========================================================= */

.event-hero {
  position: relative;
  min-height: 690px;
  overflow: hidden;
  background: var(--yellow);
  border-bottom: 2px solid var(--ink);
  isolation: isolate;
}

.hero-grid {
  position: absolute;
  inset: 0;
  opacity: 0.16;
  background-image:
    linear-gradient(var(--ink) 1px, transparent 1px),
    linear-gradient(90deg, var(--ink) 1px, transparent 1px);
  background-size: 48px 48px;
  transform: perspective(500px) rotateX(55deg) scale(1.6) translateY(30%);
  transform-origin: bottom;
}

.hero-sun {
  position: absolute;
  width: 460px;
  height: 460px;
  right: -80px;
  top: -150px;
  border-radius: 50%;
  background: var(--pink);
  border: 2px solid var(--ink);
  z-index: -1;
}

.hero-cloud {
  position: absolute;
  width: 130px;
  height: 45px;
  border: 2px solid var(--ink);
  border-radius: 100px;
  background: #fff;
  opacity: 0.8;
}

.hero-cloud::before,
.hero-cloud::after {
  position: absolute;
  content: "";
  border: 2px solid var(--ink);
  border-bottom: 0;
  border-radius: 100px 100px 0 0;
  background: inherit;
}

.hero-cloud::before {
  width: 45px;
  height: 35px;
  left: 22px;
  bottom: 20px;
}

.hero-cloud::after {
  width: 55px;
  height: 43px;
  right: 22px;
  bottom: 20px;
}

.cloud-one {
  left: 7%;
  top: 13%;
  transform: scale(0.75);
}

.cloud-two {
  left: 47%;
  top: 7%;
  transform: scale(0.45);
}

.hero-inner {
  position: relative;
  z-index: 2;
  display: grid;
  grid-template-columns: 1.1fr 0.9fr;
  align-items: center;
  min-height: 620px;
  gap: 40px;
}

.hero-copy {
  padding: 65px 0 100px;
}

.eyebrow {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 24px;
  padding: 8px 13px;
  border: 2px solid var(--ink);
  border-radius: 999px;
  background: #fff;
  font-size: 0.7rem;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  box-shadow: 4px 4px 0 var(--ink);
}

.live-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: #55b94a;
  border: 1px solid var(--ink);
}

.hero-copy h1 {
  max-width: 750px;
  margin: 0;
  font-family: var(--font-display);
  font-size: clamp(4.3rem, 8vw, 8.4rem);
  line-height: 0.82;
  letter-spacing: -0.07em;
}

.scribble-word {
  position: relative;
  display: inline-block;
}

.scribble-word::after {
  position: absolute;
  left: -3%;
  right: -4%;
  bottom: -4px;
  height: 18px;
  content: "";
  border-top: 6px solid var(--pink);
  border-radius: 50%;
  transform: rotate(-2deg);
}

.hero-highlight {
  color: #fff;
  -webkit-text-stroke: 2px var(--ink);
  text-shadow: 7px 7px 0 var(--ink);
}

.hero-copy > p {
  max-width: 570px;
  margin: 35px 0 25px;
  font-size: 1.02rem;
  line-height: 1.7;
}

.hero-actions {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
}

.hero-button,
.dark-button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 9px;
  min-height: 48px;
  padding: 0 20px;
  border: 2px solid var(--ink);
  border-radius: 10px;
  font-weight: 900;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
}

.hero-button:hover,
.dark-button:hover {
  transform: translate(-2px, -2px);
  box-shadow: 5px 5px 0 var(--ink);
}

.hero-button.primary {
  background: var(--ink);
  color: #fff;
}

.hero-button.secondary {
  background: #fff;
  color: var(--ink);
}

.hero-note {
  display: flex;
  align-items: center;
  gap: 8px;
  max-width: 550px;
  margin-top: 20px;
  color: rgba(23, 23, 23, 0.65);
  font-size: 0.72rem;
  font-weight: 700;
}

.hero-stage {
  position: relative;
  height: 540px;
  perspective: 1200px;
}

.event-ticket {
  position: absolute;
  width: min(370px, 70%);
  aspect-ratio: 0.7;
  top: 50%;
  left: 50%;
  padding: 28px;
  border: 3px solid var(--ink);
  border-radius: 25px;
  background: var(--blue);
  box-shadow: 15px 18px 0 var(--ink);
  transform-style: preserve-3d;
  transform-origin: center;
  transition: transform 0.12s ease-out;
}

.event-ticket::before {
  position: absolute;
  inset: 12px;
  content: "";
  border: 2px dashed var(--ink);
  border-radius: 17px;
  pointer-events: none;
}

.ticket-hole {
  position: absolute;
  width: 27px;
  height: 27px;
  top: 48%;
  border: 3px solid var(--ink);
  border-radius: 50%;
  background: var(--yellow);
  transform: translateY(-50%);
}

.hole-one {
  left: -16px;
}

.hole-two {
  right: -16px;
}

.ticket-top {
  position: relative;
  display: flex;
  justify-content: space-between;
  font-size: 0.7rem;
  font-weight: 900;
}

.ticket-mark {
  font-size: 1.7rem;
}

.ticket-title {
  position: relative;
  margin-top: 80px;
  font-family: var(--font-display);
  font-size: clamp(2.6rem, 5vw, 4.4rem);
  line-height: 0.8;
  letter-spacing: -0.06em;
}

.ticket-meta {
  position: absolute;
  bottom: 82px;
  left: 28px;
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
  max-width: 210px;
}

.ticket-meta span {
  padding: 5px 8px;
  border: 2px solid var(--ink);
  border-radius: 999px;
  background: #fff;
  font-size: 0.6rem;
  font-weight: 900;
}

.ticket-barcode {
  position: absolute;
  right: 28px;
  bottom: 27px;
  display: flex;
  align-items: stretch;
  height: 38px;
  gap: 3px;
}

.ticket-barcode i {
  width: 3px;
  background: var(--ink);
}

.ticket-barcode i:nth-child(2n) {
  width: 6px;
}

.floating-doodle,
.floating-label {
  position: absolute;
  z-index: 3;
  font-weight: 900;
}

.doodle-smile {
  top: 17%;
  left: 5%;
  font-size: 5rem;
  transform: rotate(-12deg);
}

.doodle-star {
  top: 20%;
  right: 5%;
  color: var(--pink);
  font-size: 4rem;
  -webkit-text-stroke: 2px var(--ink);
}

.doodle-arrow {
  bottom: 15%;
  left: 3%;
  font-size: 4rem;
  transform: rotate(-20deg);
}

.floating-label {
  padding: 9px 12px;
  border: 2px solid var(--ink);
  background: #fff;
  font-size: 0.65rem;
  line-height: 1;
  box-shadow: 4px 4px 0 var(--ink);
}

.label-one {
  right: 0;
  top: 44%;
  transform: rotate(7deg);
}

.label-two {
  left: 12%;
  bottom: 13%;
  transform: rotate(-7deg);
}

.hero-bottom {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 4;
  border-top: 2px solid var(--ink);
  background: rgba(255, 255, 255, 0.24);
}

.hero-bottom-inner {
  display: flex;
  align-items: center;
  justify-content: space-between;
  min-height: 48px;
  font-size: 0.65rem;
  font-weight: 900;
  letter-spacing: 0.12em;
}

.scroll-line {
  flex: 1;
  height: 1px;
  margin: 0 20px;
  background: var(--ink);
}

/* =========================================================
   SEARCH
   ========================================================= */

.search-section {
  position: relative;
  z-index: 10;
  padding: 35px 0 20px;
  background: var(--paper);
}

.search-wrap {
  display: flex;
  align-items: center;
  gap: 14px;
  min-height: 72px;
  padding: 0 18px;
  border: 2px solid var(--ink);
  border-radius: 16px;
  background: #fff;
  box-shadow: 6px 6px 0 var(--ink);
}

.search-icon {
  display: grid;
  place-items: center;
}

.search-wrap input {
  min-width: 0;
  flex: 1;
  border: 0;
  outline: 0;
  background: transparent;
  color: var(--ink);
  font-size: 1rem;
}

.search-count {
  white-space: nowrap;
  font-size: 0.7rem;
  font-weight: 900;
}

.icon-button {
  width: 36px;
  height: 36px;
  display: grid;
  place-items: center;
  border: 0;
  border-radius: 50%;
  background: var(--paper-2);
  cursor: pointer;
}

.search-examples {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  gap: 7px;
  margin-top: 13px;
  color: var(--muted);
  font-size: 0.72rem;
}

.search-examples button {
  padding: 5px 10px;
  border: 1px solid var(--line);
  border-radius: 999px;
  background: transparent;
  cursor: pointer;
}

.search-examples button:hover {
  background: var(--yellow);
  border-color: var(--ink);
}

/* =========================================================
   RESULT DRAWERS
   ========================================================= */

.result-drawer {
  margin-top: 22px;
  padding: 28px;
  border: 2px solid var(--ink);
  border-radius: 18px;
  background: #fff;
  box-shadow: 7px 7px 0 var(--ink);
}

.drawer-heading {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  gap: 20px;
  margin-bottom: 22px;
}

.drawer-heading h2 {
  margin: 0;
  font-family: var(--font-display);
  font-size: 2rem;
}

.query-pill {
  padding: 7px 12px;
  border: 1px solid var(--ink);
  border-radius: 999px;
  background: var(--yellow);
  font-size: 0.75rem;
  font-weight: 900;
}

.result-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 14px;
}

.mini-event-card {
  padding: 20px;
  border: 2px solid var(--ink);
  border-radius: 15px;
  background: var(--paper);
  transition: transform 0.2s, box-shadow 0.2s;
}

.mini-event-card:hover {
  transform: translate(-3px, -3px) rotate(-0.4deg);
  box-shadow: 5px 5px 0 var(--ink);
}

.mini-event-top {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 8px;
  margin-bottom: 15px;
}

.mini-event-city {
  font-size: 0.68rem;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: 0.08em;
}

.upcoming-chip,
.past-chip {
  padding: 4px 7px;
  border: 1px solid var(--ink);
  border-radius: 999px;
  font-size: 0.55rem;
  font-weight: 900;
}

.upcoming-chip {
  background: var(--green);
}

.past-chip {
  background: var(--paper-2);
}

.mini-event-card h3 {
  margin: 0 0 12px;
  font-family: var(--font-display);
  font-size: 1.35rem;
  line-height: 1;
}

.mini-event-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  color: var(--muted);
  font-size: 0.7rem;
}

.mini-event-meta span {
  display: inline-flex;
  align-items: center;
  gap: 5px;
}

.mini-event-card > p {
  margin: 14px 0;
  color: var(--muted);
  font-size: 0.78rem;
  line-height: 1.55;
}

.mini-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 5px;
}

.mini-tags span {
  padding: 4px 7px;
  border-radius: 5px;
  background: #fff;
  font-size: 0.62rem;
}

.mini-event-links {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  margin-top: 14px;
}

.mini-event-links a {
  color: var(--ink);
  font-size: 0.7rem;
  font-weight: 900;
}

.result-empty {
  padding: 35px 10px 10px;
  text-align: center;
}

.empty-star {
  font-size: 2rem;
  margin-bottom: 5px;
}

.result-empty h3 {
  margin: 0 0 6px;
  font-family: var(--font-display);
  font-size: 1.5rem;
}

.result-empty p {
  margin: 0;
  color: var(--muted);
}

/* =========================================================
   CITIES
   ========================================================= */

.cities-section {
  background: var(--paper);
}

.city-mosaic {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: 210px;
  gap: 14px;
}

.city-card {
  position: relative;
  min-width: 0;
  overflow: hidden;
  padding: 0;
  border: 2px solid var(--ink);
  border-radius: 20px;
  background: var(--ink);
  cursor: pointer;
  text-align: left;
  box-shadow: 5px 5px 0 var(--ink);
  transition: transform 0.25s, box-shadow 0.25s;
}

.city-card:hover {
  transform: translate(-3px, -5px) rotate(-0.5deg);
  box-shadow: 9px 10px 0 var(--ink);
}

.city-card img {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.5s;
}

.city-card:hover img {
  transform: scale(1.08);
}

.city-large {
  grid-column: span 2;
  grid-row: span 2;
}

.city-tall {
  grid-row: span 2;
}

.city-shade {
  position: absolute;
  inset: 0;
  background:
    linear-gradient(to top, rgba(0, 0, 0, 0.8), rgba(0, 0, 0, 0.05) 70%),
    linear-gradient(120deg, rgba(0, 0, 0, 0.35), transparent 55%);
}

.city-badge {
  position: absolute;
  top: 15px;
  left: 15px;
  padding: 6px 9px;
  border: 2px solid var(--ink);
  border-radius: 999px;
  background: var(--yellow);
  color: var(--ink);
  font-size: 0.58rem;
  font-weight: 900;
  text-transform: uppercase;
}

.city-doodle {
  position: absolute;
  right: 16px;
  top: 13px;
  color: #fff;
  font-size: 2rem;
  text-shadow: 3px 3px 0 var(--ink);
}

.city-info {
  position: absolute;
  right: 18px;
  left: 18px;
  bottom: 18px;
  color: #fff;
}

.city-info span,
.city-info small {
  display: block;
  font-size: 0.65rem;
  font-weight: 800;
  opacity: 0.82;
}

.city-info strong {
  display: block;
  margin: 4px 0;
  font-family: var(--font-display);
  font-size: clamp(1.8rem, 3.2vw, 3.4rem);
  line-height: 0.9;
}

.simple-empty {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  gap: 7px;
  min-height: 220px;
  border: 2px dashed var(--ink);
  border-radius: 18px;
}

/* =========================================================
   INTERESTS
   ========================================================= */

.interests-section {
  background: var(--blue);
  border-top: 2px solid var(--ink);
  border-bottom: 2px solid var(--ink);
}

.interest-row {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 12px;
}

.interest-card {
  position: relative;
  min-height: 245px;
  padding: 22px;
  border: 2px solid var(--ink);
  border-radius: 17px;
  background: #fff;
  text-align: left;
  cursor: pointer;
  box-shadow: 5px 5px 0 var(--ink);
  transition: transform 0.22s, box-shadow 0.22s;
}

.interest-card:hover {
  transform: translate(-3px, -4px) rotate(-0.6deg);
  box-shadow: 8px 9px 0 var(--ink);
}

.interest-card.selected {
  background: var(--yellow);
  transform: translate(-3px, -4px);
  box-shadow: 8px 9px 0 var(--ink);
}

.interest-number {
  position: absolute;
  top: 15px;
  right: 17px;
  font-size: 0.65rem;
  font-weight: 900;
}

.interest-icon {
  width: 48px;
  height: 48px;
  display: grid;
  place-items: center;
  margin-bottom: 38px;
  border: 2px solid var(--ink);
  border-radius: 13px;
  background: var(--paper);
}

.interest-card strong {
  display: block;
  font-family: var(--font-display);
  font-size: 1.45rem;
}

.interest-card p {
  max-width: 220px;
  margin: 8px 0 0;
  color: var(--muted);
  font-size: 0.75rem;
  line-height: 1.5;
}

.interest-arrow {
  position: absolute;
  right: 18px;
  bottom: 16px;
  font-size: 1.3rem;
  font-weight: 900;
}

.interest-drawer {
  margin-top: 28px;
  background: var(--paper);
}

.text-button {
  border: 0;
  background: transparent;
  color: var(--ink);
  font-size: 0.72rem;
  font-weight: 900;
  cursor: pointer;
}


/* =========================================================
   INTEREST RESULTS — COMPACT MEETUP DISCOVERY LIST
   ========================================================= */

.interest-drawer {
  margin-top: 18px;
  padding: 20px;
  border-radius: 20px;
  background: rgba(255, 255, 255, 0.94);
  box-shadow: 5px 5px 0 var(--ink);
}

.interest-drawer .drawer-heading {
  align-items: center;
  margin-bottom: 14px;
}

.interest-drawer .drawer-heading h2 {
  font-size: 1.55rem;
  line-height: 1;
}

.interest-drawer .drawer-heading .mini-label {
  font-size: 0.58rem;
}

.interest-result-tools {
  display: flex;
  align-items: center;
  gap: 12px;
  color: var(--muted);
  font-size: 0.65rem;
  font-weight: 800;
}

.interest-result-tools .text-button {
  padding: 7px 10px;
  border: 1px solid rgba(20, 20, 20, 0.22);
  border-radius: 999px;
  background: #fff;
  transition: background 0.18s ease, color 0.18s ease;
}

.interest-result-tools .text-button:hover {
  background: var(--ink);
  color: #fff;
}

.interest-drawer .result-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 9px;
}

.interest-drawer .mini-event-card {
  display: grid;
  grid-template-columns: minmax(125px, 0.28fr) minmax(0, 1fr) auto;
  grid-template-areas:
    "top title links"
    "top meta links"
    "top about links"
    "top tags links";
  align-items: center;
  column-gap: 18px;
  row-gap: 5px;
  min-height: 112px;
  padding: 15px 16px;
  border: 1.5px solid var(--ink);
  border-radius: 14px;
  background: #fff;
  box-shadow: none;
  transition: transform 0.18s ease, box-shadow 0.18s ease, background 0.18s ease;
}

.interest-drawer .mini-event-card:hover {
  transform: translateX(3px);
  box-shadow: 4px 4px 0 var(--ink);
  background: #fffdf5;
}

.interest-drawer .mini-event-top {
  grid-area: top;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: center;
  gap: 7px;
  align-self: stretch;
  margin: 0;
  padding-right: 16px;
  border-right: 1px dashed rgba(20, 20, 20, 0.28);
}

.interest-drawer .mini-event-city {
  font-size: 0.62rem;
  line-height: 1.2;
}

.interest-drawer .upcoming-chip,
.interest-drawer .past-chip {
  font-size: 0.5rem;
  padding: 3px 6px;
}

.interest-drawer .mini-event-card h3 {
  grid-area: title;
  margin: 0;
  max-width: 100%;
  overflow: hidden;
  font-size: 1.08rem;
  line-height: 1.15;
  display: -webkit-box;
  -webkit-line-clamp: 1;
  -webkit-box-orient: vertical;
}

.interest-drawer .mini-event-meta {
  grid-area: meta;
  gap: 10px;
  min-width: 0;
  font-size: 0.64rem;
}

.interest-drawer .mini-event-meta span {
  min-width: 0;
  max-width: 240px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.interest-drawer .mini-event-card > p {
  grid-area: about;
  max-width: 720px;
  margin: 0;
  overflow: hidden;
  color: var(--muted);
  font-size: 0.69rem;
  line-height: 1.35;
  display: -webkit-box;
  -webkit-line-clamp: 1;
  -webkit-box-orient: vertical;
}

.interest-drawer .mini-tags {
  grid-area: tags;
  min-width: 0;
  max-height: 22px;
  overflow: hidden;
}

.interest-drawer .mini-tags span {
  padding: 3px 6px;
  font-size: 0.56rem;
  border: 1px solid rgba(20, 20, 20, 0.1);
}

.interest-drawer .mini-event-links {
  grid-area: links;
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  justify-content: center;
  gap: 6px;
  min-width: 72px;
  margin: 0;
  padding-left: 12px;
  border-left: 1px dashed rgba(20, 20, 20, 0.28);
}

.interest-drawer .mini-event-links a {
  white-space: nowrap;
  font-size: 0.64rem;
}

.interest-drawer .result-empty {
  padding: 24px 10px 8px;
}

@media (max-width: 820px) {
  .interest-drawer .mini-event-card {
    grid-template-columns: 1fr auto;
    grid-template-areas:
      "top links"
      "title links"
      "meta links"
      "about links"
      "tags links";
    column-gap: 12px;
  }

  .interest-drawer .mini-event-top {
    flex-direction: row;
    align-items: center;
    gap: 8px;
    padding: 0 0 7px;
    border-right: 0;
    border-bottom: 1px dashed rgba(20, 20, 20, 0.28);
  }
}

@media (max-width: 560px) {
  .interest-drawer {
    padding: 14px;
    box-shadow: 4px 4px 0 var(--ink);
  }

  .interest-drawer .drawer-heading {
    margin-bottom: 11px;
  }

  .interest-result-tools {
    gap: 7px;
  }

  .interest-result-tools > span {
    display: none;
  }

  .interest-drawer .drawer-heading h2 {
    font-size: 1.3rem;
  }

  .interest-drawer .mini-event-card {
    grid-template-columns: 1fr;
    grid-template-areas:
      "top"
      "title"
      "meta"
      "about"
      "tags"
      "links";
    padding: 13px;
  }

  .interest-drawer .mini-event-top {
    padding-bottom: 8px;
  }

  .interest-drawer .mini-event-card > p {
    -webkit-line-clamp: 2;
  }

  .interest-drawer .mini-event-links {
    flex-direction: row;
    align-items: center;
    justify-content: flex-start;
    padding: 8px 0 0;
    border-left: 0;
    border-top: 1px dashed rgba(20, 20, 20, 0.28);
  }
}

/* =========================================================
   UPCOMING
   ========================================================= */

.upcoming-section {
  background: var(--paper);
}

.event-board {
  display: grid;
  gap: 12px;
}

.event-card {
  display: grid;
  grid-template-columns: 100px 1fr auto;
  align-items: center;
  gap: 24px;
  padding: 20px;
  border: 2px solid var(--ink);
  border-radius: 18px;
  background: #fff;
  box-shadow: 4px 4px 0 var(--ink);
}

.event-card:nth-child(2n) {
  background: var(--green);
}

.event-card:nth-child(3n) {
  background: var(--pink);
}

.event-date {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  min-height: 92px;
  border: 2px solid var(--ink);
  border-radius: 12px;
  background: var(--yellow);
}

.event-date span {
  font-size: 0.6rem;
  font-weight: 900;
}

.event-date strong {
  font-family: var(--font-display);
  font-size: 2.6rem;
  line-height: 0.9;
}

.event-topline {
  display: flex;
  gap: 12px;
  margin-bottom: 6px;
  font-size: 0.65rem;
  font-weight: 900;
  text-transform: uppercase;
}

.event-topline span:last-child {
  color: var(--muted);
}

.event-main h3 {
  margin: 0;
  font-family: var(--font-display);
  font-size: 1.8rem;
}

.event-main p {
  max-width: 700px;
  margin: 8px 0;
  color: var(--muted);
  font-size: 0.78rem;
  line-height: 1.55;
}

.event-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 13px;
  color: var(--muted);
  font-size: 0.7rem;
}

.event-meta span {
  display: inline-flex;
  align-items: center;
  gap: 5px;
}

.event-go {
  padding: 10px 15px;
  border: 2px solid var(--ink);
  border-radius: 9px;
  color: var(--ink);
  background: #fff;
  font-size: 0.72rem;
  font-weight: 900;
  text-decoration: none;
}

.empty-board {
  padding: 60px 20px;
  border: 2px dashed var(--ink);
  border-radius: 18px;
  text-align: center;
}

.empty-face {
  font-size: 3rem;
  margin-bottom: 8px;
}

.empty-board h3 {
  margin: 0 0 7px;
  font-family: var(--font-display);
  font-size: 2rem;
}

.empty-board p {
  margin: 0 0 20px;
  color: var(--muted);
}

/* =========================================================
   SUGGESTION
   ========================================================= */

.suggestion-section {
  padding-top: 35px;
  background: var(--paper-2);
}

.suggest-card {
  position: relative;
  display: grid;
  grid-template-columns: 0.8fr 1.2fr;
  align-items: center;
  min-height: 420px;
  overflow: hidden;
  border: 2px solid var(--ink);
  border-radius: 25px;
  background: var(--pink);
  box-shadow: 9px 9px 0 var(--ink);
}

.suggest-art {
  position: relative;
  height: 100%;
  min-height: 420px;
  overflow: hidden;
  border-right: 2px solid var(--ink);
  background: var(--yellow);
}

.suggest-face {
  position: absolute;
  width: 190px;
  height: 190px;
  left: 50%;
  top: 50%;
  border: 4px solid var(--ink);
  border-radius: 45% 55% 48% 52%;
  background: #fff;
  transform: translate(-50%, -50%) rotate(-7deg);
  box-shadow: 9px 9px 0 var(--ink);
}

.suggest-face span {
  position: absolute;
  width: 15px;
  height: 23px;
  top: 60px;
  border: 3px solid var(--ink);
  border-radius: 50%;
  background: var(--ink);
}

.suggest-face span:first-child {
  left: 50px;
}

.suggest-face span:nth-child(2) {
  right: 50px;
}

.suggest-face i {
  position: absolute;
  width: 65px;
  height: 30px;
  left: 50%;
  bottom: 43px;
  border-bottom: 4px solid var(--ink);
  border-radius: 50%;
  transform: translateX(-50%);
}

.speech {
  position: absolute;
  padding: 9px 13px;
  border: 2px solid var(--ink);
  background: #fff;
  font-size: 0.7rem;
  font-weight: 900;
  line-height: 0.9;
  box-shadow: 4px 4px 0 var(--ink);
}

.speech-one {
  left: 15%;
  top: 18%;
  transform: rotate(-10deg);
}

.speech-two {
  right: 8%;
  bottom: 18%;
  transform: rotate(7deg);
}

.suggest-copy {
  padding: 50px;
}

.suggest-copy h2 {
  margin: 0 0 15px;
  font-family: var(--font-display);
  font-size: clamp(2.6rem, 5vw, 5rem);
  line-height: 0.88;
  letter-spacing: -0.05em;
}

.suggest-copy h2 span {
  color: #fff;
  -webkit-text-stroke: 2px var(--ink);
}

.suggest-copy p {
  max-width: 570px;
  margin-bottom: 24px;
  color: rgba(23, 23, 23, 0.72);
  line-height: 1.65;
}

.dark-button {
  background: var(--ink);
  color: #fff;
}

.suggest-form {
  margin-top: 18px;
  padding: 30px;
  border: 2px solid var(--ink);
  border-radius: 18px;
  background: #fff;
  box-shadow: 6px 6px 0 var(--ink);
}

.form-title h3 {
  margin: 0 0 25px;
  font-family: var(--font-display);
  font-size: 2rem;
}

.form-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 16px;
}

.form-grid label {
  display: flex;
  flex-direction: column;
  gap: 7px;
}

.form-grid label.full {
  grid-column: 1 / -1;
}

.form-grid label span {
  font-size: 0.68rem;
  font-weight: 900;
  text-transform: uppercase;
}

.form-grid input,
.form-grid textarea {
  width: 100%;
  border: 2px solid var(--ink);
  border-radius: 9px;
  outline: 0;
  padding: 12px 13px;
  background: var(--paper);
  color: var(--ink);
  resize: vertical;
}

.form-grid input:focus,
.form-grid textarea:focus {
  background: var(--yellow);
}

.form-bottom {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  margin-top: 20px;
  padding-top: 18px;
  border-top: 1px solid var(--line);
}

.form-bottom > span {
  color: var(--muted);
  font-size: 0.72rem;
}

/* =========================================================
   ARCHIVE
   ========================================================= */

.archive-section {
  padding: 30px 0 90px;
  background: var(--paper-2);
}

.archive-trigger {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 18px 20px;
  border: 2px solid var(--ink);
  border-radius: 12px;
  background: #fff;
  cursor: pointer;
  box-shadow: 4px 4px 0 var(--ink);
  font-weight: 900;
}

.archive-trigger span {
  display: inline-flex;
  align-items: center;
  gap: 9px;
}

.archive-trigger strong {
  font-size: 1.5rem;
}

.archive-drawer {
  background: var(--paper);
}

/* =========================================================
   RESPONSIVE
   ========================================================= */

@media (max-width: 1000px) {
  .hero-inner {
    grid-template-columns: 1fr;
  }

  .hero-copy {
    padding-bottom: 20px;
  }

  .hero-stage {
    position: absolute;
    width: 48%;
    right: 0;
    top: 100px;
    opacity: 0.6;
  }

  .hero-copy {
    position: relative;
    z-index: 4;
    max-width: 720px;
  }

  .result-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }

  .city-mosaic {
    grid-template-columns: repeat(3, 1fr);
  }

  .interest-row {
    grid-template-columns: repeat(2, 1fr);
  }

  .suggest-card {
    grid-template-columns: 0.65fr 1.35fr;
  }
}

  .past-card {
    grid-template-columns: 68px 1fr;
  }

  .past-link {
    grid-column: 2;
    justify-self: start;
  }

@media (max-width: 700px) {
  .container {
    width: min(100% - 24px, 1180px);
  }

  .section {
    padding: 75px 0;
  }

  .event-hero {
    min-height: 650px;
  }

  .hero-inner {
    min-height: 600px;
  }

  .hero-copy {
    padding-top: 75px;
  }

  .hero-copy h1 {
    font-size: clamp(3.7rem, 17vw, 6rem);
  }

  .hero-stage {
    display: none;
  }

  .hero-bottom-inner span:nth-last-child(1) {
    display: none;
  }

  .section-heading {
    display: block;
  }

  .section-heading p {
    margin-top: 18px;
  }

  .result-grid {
    grid-template-columns: 1fr;
  }

  .city-mosaic {
    grid-template-columns: repeat(2, 1fr);
    grid-auto-rows: 175px;
  }

  .city-large {
    grid-column: span 2;
    grid-row: span 1;
  }

  .city-tall {
    grid-row: span 1;
  }

  .city-info strong {
    font-size: 2rem;
  }

  .interest-row {
    grid-template-columns: 1fr;
  }

  .event-card {
    grid-template-columns: 70px 1fr;
  }

  .event-go {
    grid-column: 2;
    justify-self: start;
  }

  .suggest-card {
    grid-template-columns: 1fr;
  }

  .suggest-art {
    min-height: 270px;
    border-right: 0;
    border-bottom: 2px solid var(--ink);
  }

  .suggest-copy {
    padding: 35px 25px;
  }

  .form-grid {
    grid-template-columns: 1fr;
  }

  .form-grid label.full {
    grid-column: auto;
  }

  .form-bottom {
    align-items: flex-start;
    flex-direction: column;
  }
}

@media (max-width: 480px) {
  .search-wrap {
    min-height: 60px;
  }

  .search-count {
    display: none;
  }

  .result-drawer {
    padding: 18px;
  }

  .drawer-heading {
    align-items: flex-start;
    flex-direction: column;
  }

  .city-mosaic {
    grid-template-columns: 1fr;
  }

  .city-large {
    grid-column: auto;
  }

  .city-card {
    min-height: 210px;
  }

  .event-card {
    grid-template-columns: 1fr;
  }

  .event-date {
    width: 76px;
  }

  .event-go {
    grid-column: auto;
  }
}

/* Past meetups use the same visual language as upcoming meetups,
   but stay compact so long descriptions never become three huge columns. */
.past-board {
  display: grid;
  gap: 12px;
}

.past-card {
  display: grid;
  grid-template-columns: 82px minmax(0, 1fr) auto;
  align-items: center;
  gap: 20px;
  padding: 17px;
  border: 2px solid var(--ink);
  border-radius: 16px;
  background: #fff;
  box-shadow: 3px 3px 0 var(--ink);
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.past-card:hover {
  transform: translate(-2px, -2px);
  box-shadow: 5px 5px 0 var(--ink);
}

.past-date {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  min-height: 76px;
  border: 2px solid var(--ink);
  border-radius: 11px;
  background: var(--paper-2);
}

.past-date span {
  font-size: 0.55rem;
  font-weight: 900;
}

.past-date strong {
  font-family: var(--font-display);
  font-size: 2rem;
  line-height: 0.9;
}

.past-topline {
  display: flex;
  justify-content: space-between;
  gap: 10px;
  margin-bottom: 5px;
  color: var(--muted);
  font-size: 0.62rem;
  font-weight: 900;
  text-transform: uppercase;
  letter-spacing: 0.06em;
}

.past-main h3 {
  margin: 0;
  font-family: var(--font-display);
  font-size: 1.3rem;
  line-height: 1.05;
}

.past-main p {
  display: -webkit-box;
  overflow: hidden;
  max-width: 800px;
  margin: 7px 0;
  color: var(--muted);
  font-size: 0.76rem;
  line-height: 1.5;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
}

.past-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  color: var(--muted);
  font-size: 0.65rem;
}

.past-meta span {
  display: inline-flex;
  align-items: center;
  gap: 5px;
}

.past-link {
  white-space: nowrap;
  padding: 9px 12px;
  border: 2px solid var(--ink);
  border-radius: 8px;
  color: var(--ink);
  background: var(--yellow);
  font-size: 0.68rem;
  font-weight: 900;
  text-decoration: none;
}

/* Render below-the-fold sections only when needed; this reduces initial
   layout/paint work without removing content or functionality. */
.cities-section,
.interests-section,
.upcoming-section,
.suggestion-section,
.archive-section {
  content-visibility: auto;
  contain-intrinsic-size: 0 700px;
}

.city-card img {
  display: block;
  content-visibility: auto;
}

</style>

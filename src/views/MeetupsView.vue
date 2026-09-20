<template>
  <div class="meetups-page">
    <!-- COMPACT INTRO -->
    <section class="meetups-intro">
      <div class="container">
        <div class="section-tag">Sundarbans Community</div>
        <h1 class="intro-title">
          Meetups <span class="tg">that bring us together.</span>
        </h1>
        <p class="intro-sub">
          Find students, events and communities around you — or suggest something you'd like to see.
        </p>
      </div>
    </section>

    <!-- SEARCH -->
    <section class="search-section">
      <div class="container">
        <div class="search-box">
          <Search :size="20" :stroke-width="1.8" />
          <input
            v-model="searchQuery"
            type="search"
            placeholder="Search meetups, cities, topics, venues..."
            aria-label="Search meetups"
          />
          <button
            v-if="searchQuery"
            class="clear-search"
            type="button"
            @click="searchQuery = ''"
            aria-label="Clear search"
          >
            <X :size="17" />
          </button>
        </div>

        <!-- SEARCH RESULTS APPEAR DIRECTLY BELOW SEARCH -->
        <div v-if="searchQuery.trim()" class="inline-results">
          <div class="result-header">
            <div>
              <span class="section-tag">Search results</span>
              <h3>
                {{ filteredSearchResults.length }}
                {{ filteredSearchResults.length === 1 ? 'meetup' : 'meetups' }} found
              </h3>
            </div>
            <span class="result-query">“{{ searchQuery }}”</span>
          </div>

          <div v-if="filteredSearchResults.length" class="meetup-results-grid">
            <article
              v-for="meetup in filteredSearchResults"
              :key="meetup.key"
              class="meetup-result-card"
            >
              <div class="result-card-top">
                <span class="result-city">{{ meetup.city }}</span>
                <span v-if="meetup.meetupNumber" class="result-number">
                  {{ meetup.meetupNumber }}
                </span>
              </div>

              <h4>{{ meetup.title }}</h4>

              <div class="result-meta">
                <span v-if="meetup.date">
                  <Calendar :size="14" />
                  {{ meetup.date }}
                </span>
                <span v-if="meetup.location">
                  <MapPin :size="14" />
                  {{ meetup.location }}
                </span>
              </div>

              <p v-if="meetup.about">
                {{ meetup.about }}
              </p>

              <div v-if="meetup.tags?.length" class="result-tags">
                <span v-for="tag in meetup.tags.slice(0, 4)" :key="tag">
                  {{ tag }}
                </span>
              </div>

              <a
                v-if="meetup.instaUrl"
                :href="meetup.instaUrl"
                target="_blank"
                rel="noopener noreferrer"
                class="result-link"
              >
                View meetup →
              </a>
            </article>
          </div>

          <div v-else class="empty-result">
            <Search :size="28" />
            <h4>No matching meetups</h4>
            <p>Try a city, topic, venue or meetup number.</p>
          </div>
        </div>
      </div>
    </section>

    <!-- CITIES - EXISTING VISUAL DESIGN PRESERVED -->
    <section class="section rs city-section" ref="gridSection">
      <div class="container">
        <div class="sec-hdr">
          <div class="section-tag">Across India</div>
          <h2 class="section-title-xl">Choose <span class="tg">your city</span></h2>
        </div>

        <div class="regions-grid">
          <div
            v-for="region in visibleRegions"
            :key="region.slug"
            class="region-card reveal"
            :class="{ featured: region.featured }"
            @mouseenter="hoveredRegion = region.slug"
            @mouseleave="hoveredRegion = null"
            @click="goToRegion(region.slug)"
          >
            <img
              :src="region.image"
              :alt="region.name"
              class="region-bg"
              loading="lazy"
            />

            <div class="region-overlay"></div>

            <div
              v-if="hoveredRegion === region.slug"
              class="region-gold-tint"
            ></div>

            <div class="region-content">
              <div class="region-top">
                <span v-if="region.badge" class="region-badge">
                  {{ region.badge }}
                </span>
              </div>

              <div class="region-bottom">
                <h3 class="region-name">{{ region.name }}</h3>
                <p class="region-members">{{ region.members }} members</p>
              </div>
            </div>
          </div>
        </div>

        <div v-if="!visibleRegions.length" class="city-empty">
          <MapPin :size="28" />
          <h3>No city found</h3>
          <p>Try searching for another city or meetup.</p>
        </div>
      </div>
    </section>

    <!-- WHAT ARE YOU LOOKING FOR -->
    <section class="section interest-section">
      <div class="container">
        <div class="sec-hdr">
          <div class="section-tag">Find your kind of meetup</div>
          <h2 class="section-title-xl">
            What are <span class="tg">you looking for?</span>
          </h2>
          <p class="sec-sub">
            Choose an interest and the relevant meetups will appear right here.
          </p>
        </div>

        <div class="interest-grid">
          <button
            v-for="interest in interests"
            :key="interest.key"
            type="button"
            class="interest-card"
            :class="{ active: activeInterest === interest.key }"
            @click="toggleInterest(interest.key)"
          >
            <div class="interest-icon">
              <component :is="interest.icon" :size="25" :stroke-width="1.6" />
            </div>

            <div>
              <h3>{{ interest.label }}</h3>
              <p>{{ interest.description }}</p>
            </div>

            <span class="interest-arrow">→</span>
          </button>
        </div>

        <!-- INTEREST RESULTS APPEAR DIRECTLY BELOW BUTTONS -->
        <div v-if="activeInterest" class="interest-results">
          <div class="result-header">
            <div>
              <span class="section-tag">Your selection</span>
              <h3>{{ activeInterestLabel }}</h3>
            </div>

            <button
              type="button"
              class="close-results"
              @click="activeInterest = null"
            >
              Clear
              <X :size="15" />
            </button>
          </div>

          <div v-if="interestResults.length" class="meetup-results-grid">
            <article
              v-for="meetup in interestResults"
              :key="meetup.key"
              class="meetup-result-card"
            >
              <div class="result-card-top">
                <span class="result-city">{{ meetup.city }}</span>
                <span v-if="meetup.meetupNumber" class="result-number">
                  {{ meetup.meetupNumber }}
                </span>
              </div>

              <h4>{{ meetup.title }}</h4>

              <div class="result-meta">
                <span v-if="meetup.date">
                  <Calendar :size="14" />
                  {{ meetup.date }}
                </span>

                <span v-if="meetup.location">
                  <MapPin :size="14" />
                  {{ meetup.location }}
                </span>
              </div>

              <p>{{ meetup.about || 'Community meetup.' }}</p>

              <div v-if="meetup.tags?.length" class="result-tags">
                <span v-for="tag in meetup.tags.slice(0, 4)" :key="tag">
                  {{ tag }}
                </span>
              </div>

              <a
                v-if="meetup.instaUrl"
                :href="meetup.instaUrl"
                target="_blank"
                rel="noopener noreferrer"
                class="result-link"
              >
                View meetup →
              </a>
            </article>
          </div>

          <div v-else class="empty-result">
            <Search :size="28" />
            <h4>No matching meetups yet</h4>
            <p>
              There aren't enough meetups in this category yet.
              Try another interest or suggest one below.
            </p>
          </div>
        </div>
      </div>
    </section>

    <!-- UPCOMING -->
    <section class="section upcoming-section">
      <div class="container">
        <div class="sec-hdr">
          <div class="section-tag">What's next</div>
          <h2 class="section-title-xl">
            Upcoming <span class="tg">Meetups</span>
          </h2>
          <p class="sec-sub">
            Meetups that are currently scheduled and haven't happened yet.
          </p>
        </div>

        <div v-if="upcomingMeetups.length" class="upcoming-grid">
          <article
            v-for="meetup in upcomingMeetups"
            :key="meetup.key"
            class="upcoming-card-small"
          >
            <div class="upcoming-small-top">
              <span class="upcoming-badge">
                <span></span>
                Upcoming
              </span>

              <span class="result-city">
                {{ meetup.city }}
              </span>
            </div>

            <h3>{{ meetup.title }}</h3>

            <div class="upcoming-details">
              <div v-if="meetup.date">
                <Calendar :size="15" />
                {{ meetup.date }}
              </div>

              <div v-if="meetup.time">
                <Clock :size="15" />
                {{ meetup.time }}
              </div>

              <div v-if="meetup.location">
                <MapPin :size="15" />
                {{ meetup.location }}
              </div>
            </div>

            <p>{{ meetup.about }}</p>

            <a
              v-if="meetup.mapsUrl"
              :href="meetup.mapsUrl"
              target="_blank"
              rel="noopener noreferrer"
              class="result-link"
            >
              Open location →
            </a>
          </article>
        </div>

        <div v-else class="no-upcoming">
          <div class="no-upcoming-icon">
            <Calendar :size="30" :stroke-width="1.6" />
          </div>

          <h3>No upcoming meetup scheduled</h3>
          <p>
            Nothing is currently scheduled. Check back later or suggest a meetup
            for the community.
          </p>
        </div>
      </div>
    </section>

    <!-- SUGGEST A MEETUP -->
    <section class="section suggest-section">
      <div class="container">
        <div class="suggest-box">
          <div class="suggest-content">
            <div class="section-tag">Make it happen</div>

            <h2>
              Suggest a <span class="tg">Meetup</span>
            </h2>

            <p>
              Have an idea for a meetup, activity, study session, tech discussion
              or casual gathering? Tell the community team about it.
            </p>
          </div>

          <button
            type="button"
            class="submit-btn"
            @click="showSuggestionForm = !showSuggestionForm"
          >
            {{ showSuggestionForm ? 'Close form' : 'Suggest a Meetup' }}
          </button>
        </div>

        <!-- FORM APPEARS DIRECTLY BELOW THE BUTTON -->
        <div v-if="showSuggestionForm" class="suggest-form">
          <div class="suggest-form-header">
            <div>
              <span class="section-tag">Your idea</span>
              <h3>Tell us about your meetup</h3>
            </div>

            <button
              type="button"
              class="form-close"
              @click="showSuggestionForm = false"
            >
              <X :size="18" />
            </button>
          </div>

          <div class="form-grid">
            <label>
              <span>Your name</span>
              <input
                v-model="suggestion.name"
                type="text"
                placeholder="Your name"
              />
            </label>

            <label>
              <span>City</span>
              <input
                v-model="suggestion.city"
                type="text"
                placeholder="e.g. Bangalore"
              />
            </label>

            <label>
              <span>Meetup idea</span>
              <input
                v-model="suggestion.title"
                type="text"
                placeholder="e.g. Weekend coding meetup"
              />
            </label>

            <label>
              <span>Preferred date</span>
              <input
                v-model="suggestion.date"
                type="date"
              />
            </label>

            <label class="full">
              <span>Tell us more</span>
              <textarea
                v-model="suggestion.description"
                rows="5"
                placeholder="What should happen at this meetup?"
              ></textarea>
            </label>
          </div>

          <div class="suggest-form-footer">
            <p>
              You can submit your idea through the community form.
            </p>

            <a
              href="https://forms.gle/iHeYQsAbsUTBHJJC6"
              target="_blank"
              rel="noopener noreferrer"
              class="submit-btn"
            >
              Submit Meetup Idea →
            </a>
          </div>
        </div>
      </div>
    </section>

    <!-- PAST MEETUPS - HIDDEN UNTIL REQUESTED -->
    <section class="section archive-section">
      <div class="container">
        <button
          type="button"
          class="archive-toggle"
          @click="showPastMeetups = !showPastMeetups"
        >
          <div>
            <span class="section-tag">Archive</span>
            <strong>
              {{ showPastMeetups ? 'Hide past meetups' : 'Explore past meetups' }}
            </strong>
          </div>

          <span class="archive-arrow" :class="{ open: showPastMeetups }">
            ↓
          </span>
        </button>

        <!-- PAST RESULTS APPEAR DIRECTLY BELOW -->
        <div v-if="showPastMeetups" class="archive-results">
          <div class="archive-filter-row">
            <span>
              {{ filteredPastMeetups.length }} past
              {{ filteredPastMeetups.length === 1 ? 'meetup' : 'meetups' }}
            </span>

            <span v-if="searchQuery">
              filtered by “{{ searchQuery }}”
            </span>
          </div>

          <div
            v-if="filteredPastMeetups.length"
            class="meetup-results-grid"
          >
            <article
              v-for="meetup in filteredPastMeetups"
              :key="meetup.key"
              class="meetup-result-card"
            >
              <div class="result-card-top">
                <span class="result-city">{{ meetup.city }}</span>

                <span v-if="meetup.meetupNumber" class="result-number">
                  {{ meetup.meetupNumber }}
                </span>
              </div>

              <h4>{{ meetup.title }}</h4>

              <div class="result-meta">
                <span v-if="meetup.date">
                  <Calendar :size="14" />
                  {{ meetup.date }}
                </span>

                <span v-if="meetup.location">
                  <MapPin :size="14" />
                  {{ meetup.location }}
                </span>
              </div>

              <p>{{ meetup.about }}</p>

              <div v-if="meetup.tags?.length" class="result-tags">
                <span v-for="tag in meetup.tags.slice(0, 4)" :key="tag">
                  {{ tag }}
                </span>
              </div>

              <a
                v-if="meetup.instaUrl"
                :href="meetup.instaUrl"
                target="_blank"
                rel="noopener noreferrer"
                class="result-link"
              >
                View meetup →
              </a>
            </article>
          </div>

          <div v-else class="empty-result">
            <Archive :size="28" />
            <h4>No past meetups found</h4>
            <p>Try changing your search.</p>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue';
import { useRouter } from 'vue-router';
import {
  Archive,
  Calendar,
  Clock,
  Code2,
  Globe2,
  Lightbulb,
  MapPin,
  Search,
  Users,
  X,
} from 'lucide-vue-next';

import { useScrollReveal } from '../composables/useAnimations.js';
import { regionConfigs } from './meetups/regionConfigs.js';

useScrollReveal();

const router = useRouter();

const hoveredRegion = ref(null);
const searchQuery = ref('');
const activeInterest = ref(null);
const showSuggestionForm = ref(false);
const showPastMeetups = ref(false);

const suggestion = ref({
  name: '',
  city: '',
  title: '',
  date: '',
  description: '',
});

/*
 * Existing city cards preserved.
 */
const imgDelhi =
  'https://res.cloudinary.com/l59gy0g2/image/upload/f_auto,q_auto:good,w_1000,c_limit/v1785911362/sundarbans/src/assets/regions/delhi.jpg';

const imgMumbai =
  'https://res.cloudinary.com/l59gy0g2/image/upload/f_auto,q_auto:good,w_1000,c_limit/v1785911367/sundarbans/src/assets/regions/mumbai.jpg';

const imgBangalore =
  'https://res.cloudinary.com/l59gy0g2/image/upload/f_auto,q_auto:good,w_1000,c_limit/v1785911358/sundarbans/src/assets/regions/bangalore.jpg';

const imgKolkata =
  'https://res.cloudinary.com/l59gy0g2/image/upload/f_auto,q_auto:good,w_1000,c_limit/v1785911364/sundarbans/src/assets/regions/kolkata.jpg';

const imgHyderabad =
  'https://res.cloudinary.com/l59gy0g2/image/upload/f_auto,q_auto:good,w_1000,c_limit/v1785911363/sundarbans/src/assets/regions/hyderabad.jpg';

const imgPatna =
  'https://res.cloudinary.com/l59gy0g2/image/upload/f_auto,q_auto:good,w_1000,c_limit/v1785911369/sundarbans/src/assets/regions/patna.jpg';

const imgChandigarh =
  'https://res.cloudinary.com/l59gy0g2/image/upload/f_auto,q_auto:good,w_1000,c_limit/v1785911359/sundarbans/src/assets/regions/chandigarh.webp';

const imgChennai =
  'https://res.cloudinary.com/l59gy0g2/image/upload/f_auto,q_auto:good,w_1000,c_limit/v1785911360/sundarbans/src/assets/regions/chennai.jpg';

const imgLucknow =
  'https://res.cloudinary.com/l59gy0g2/image/upload/f_auto,q_auto:good,w_1000,c_limit/v1785911366/sundarbans/src/assets/regions/lucknow.jpg';

const regions = [
  {
    slug: 'delhi-ncr',
    configSlug: 'delhi',
    name: 'Delhi-NCR',
    members: '320+',
    image: imgDelhi,
    badge: 'Most Active',
    featured: true,
  },
  {
    slug: 'mumbai',
    configSlug: 'mumbai',
    name: 'Mumbai',
    members: '450+',
    image: imgMumbai,
    badge: 'Largest Chapter',
  },
  {
    slug: 'bangalore',
    configSlug: 'bangalore',
    name: 'Bangalore',
    members: '390+',
    image: imgBangalore,
    badge: null,
  },
  {
    slug: 'kolkata',
    configSlug: 'kolkata',
    name: 'Kolkata',
    members: '280+',
    image: imgKolkata,
    badge: null,
  },
  {
    slug: 'hyderabad',
    configSlug: 'hyderabad',
    name: 'Hyderabad',
    members: '210+',
    image: imgHyderabad,
    badge: null,
  },
  {
    slug: 'patna',
    configSlug: 'patna',
    name: 'Patna',
    members: '180+',
    image: imgPatna,
    badge: null,
  },
  {
    slug: 'chandigarh',
    configSlug: 'chandigarh',
    name: 'Chandigarh',
    members: '120+',
    image: imgChandigarh,
    badge: 'Rising Chapter',
  },
  {
    slug: 'chennai',
    configSlug: 'chennai',
    name: 'Chennai',
    members: '150+',
    image: imgChennai,
    badge: null,
  },
  {
    slug: 'lucknow',
    configSlug: 'lucknow',
    name: 'Lucknow',
    members: '110+',
    image: imgLucknow,
    badge: null,
  },
];

const interests = [
  {
    key: 'learn',
    label: 'Learn',
    description: 'Study sessions, discussions, workshops and knowledge sharing.',
    icon: Lightbulb,
  },
  {
    key: 'build',
    label: 'Build',
    description: 'Projects, coding, technology, startups and collaboration.',
    icon: Code2,
  },
  {
    key: 'people',
    label: 'Meet People',
    description: 'Connect with students, friends, peers and new members.',
    icon: Users,
  },
  {
    key: 'community',
    label: 'Community',
    description: 'Chapter activities, social gatherings and community events.',
    icon: Globe2,
  },
];

function goToRegion(slug) {
  router.push('/meetups/' + slug);
}

function toggleInterest(key) {
  activeInterest.value =
    activeInterest.value === key ? null : key;
}

const activeInterestLabel = computed(() => {
  const item = interests.find(
    (interest) => interest.key === activeInterest.value,
  );

  return item?.label || '';
});

/*
 * Flatten the existing region data.
 *
 * regionConfigs already contains the normalized pastMeetups data
 * used by the individual city meetup pages.
 */
const allMeetups = computed(() => {
  const result = [];

  Object.entries(regionConfigs).forEach(([cityKey, config]) => {
    const city =
      config.heroTitle ||
      cityKey.charAt(0).toUpperCase() + cityKey.slice(1);

    if (config.upcoming) {
      result.push({
        ...config.upcoming,
        key: `${cityKey}-upcoming`,
        city,
        type: 'upcoming',
        title: config.upcoming.name || `${city} Meetup`,
        location:
          config.upcoming.venue ||
          config.upcoming.address1 ||
          '',
        about:
          config.upcoming.about ||
          'Upcoming community meetup.',
        tags: config.upcoming.tags || [],
      });
    }

    (config.pastMeetups || []).forEach((meetup) => {
      result.push({
        ...meetup,
        key: `${cityKey}-${meetup.id}`,
        city,
        type: 'past',
      });
    });
  });

  return result;
});

const upcomingMeetups = computed(() =>
  allMeetups.value.filter((meetup) => meetup.type === 'upcoming'),
);

const pastMeetups = computed(() =>
  allMeetups.value.filter((meetup) => meetup.type === 'past'),
);

/*
 * Search is intentionally flexible.
 *
 * Example:
 *   bangalore       -> Bangalore results
 *   tech            -> technology-related results
 *   cafe            -> cafe/venue results
 *   bangalore tech  -> results matching BOTH words
 */
function searchableText(meetup) {
  return [
    meetup.city,
    meetup.title,
    meetup.location,
    meetup.about,
    meetup.date,
    meetup.time,
    meetup.meetupNumber,
    ...(meetup.tags || []),
  ]
    .filter(Boolean)
    .join(' ')
    .toLowerCase();
}

function matchesSearch(meetup, query) {
  const terms = query
    .toLowerCase()
    .trim()
    .split(/\s+/)
    .filter(Boolean);

  if (!terms.length) return true;

  const text = searchableText(meetup);

  return terms.every((term) => text.includes(term));
}

const filteredSearchResults = computed(() => {
  if (!searchQuery.value.trim()) return [];

  return allMeetups.value
    .filter((meetup) => matchesSearch(meetup, searchQuery.value))
    .slice(0, 24);
});

/*
 * Interest filtering uses the actual meetup title,
 * description and tags rather than fake hard-coded events.
 */
const interestKeywords = {
  learn: [
    'learn',
    'learning',
    'study',
    'education',
    'academic',
    'discussion',
    'workshop',
    'session',
    'knowledge',
    'exam',
    'doubt',
    'talk',
    'lecture',
  ],

  build: [
    'build',
    'building',
    'project',
    'coding',
    'code',
    'developer',
    'development',
    'technology',
    'tech',
    'ai',
    'data',
    'startup',
    'hack',
    'product',
    'programming',
  ],

  people: [
    'network',
    'networking',
    'social',
    'connect',
    'connection',
    'friends',
    'meet',
    'people',
    'cafe',
    'hangout',
    'gathering',
  ],

  community: [
    'community',
    'chapter',
    'student',
    'society',
    'house',
    'sundarbans',
    'iitm',
    'iit madras',
    'meetup',
    'event',
    'collaboration',
  ],
};

function matchesInterest(meetup, interest) {
  const keywords = interestKeywords[interest] || [];
  const text = searchableText(meetup);

  return keywords.some((keyword) => text.includes(keyword));
}

const interestResults = computed(() => {
  if (!activeInterest.value) return [];

  let results = allMeetups.value.filter((meetup) =>
    matchesInterest(meetup, activeInterest.value),
  );

  /*
   * If a search is also active, apply it here.
   * This means:
   * Learn + Bangalore
   * gives Learn meetups in Bangalore.
   */
  if (searchQuery.value.trim()) {
    results = results.filter((meetup) =>
      matchesSearch(meetup, searchQuery.value),
    );
  }

  return results.slice(0, 24);
});

const filteredPastMeetups = computed(() => {
  if (!searchQuery.value.trim()) {
    return pastMeetups.value;
  }

  return pastMeetups.value.filter((meetup) =>
    matchesSearch(meetup, searchQuery.value),
  );
});

const visibleRegions = computed(() => {
  const query = searchQuery.value.trim().toLowerCase();

  if (!query) return regions;

  const terms = query.split(/\s+/).filter(Boolean);

  return regions.filter((region) => {
    const regionMeetups = allMeetups.value.filter(
      (meetup) =>
        meetup.city.toLowerCase().includes(region.name.toLowerCase()) ||
        meetup.city.toLowerCase().includes(region.configSlug),
    );

    const regionText = [
      region.name,
      region.slug,
      region.members,
      ...regionMeetups.flatMap((meetup) => [
        meetup.title,
        meetup.location,
        meetup.about,
        ...(meetup.tags || []),
      ]),
    ]
      .filter(Boolean)
      .join(' ')
      .toLowerCase();

    return terms.every((term) => regionText.includes(term));
  });
});
</script>

<style scoped>
/* =========================
   COMPACT INTRO
========================= */

.meetups-intro {
  padding: 110px 0 42px;
  border-bottom: 1px solid var(--border);
}

.intro-title {
  margin: 10px 0 12px;
  font-family: var(--font-display);
  font-size: clamp(2.5rem, 5vw, 4.5rem);
  line-height: 1;
  font-weight: 700;
}

.intro-sub {
  max-width: 650px;
  color: var(--text2);
  line-height: 1.7;
  font-size: 1rem;
}

/* =========================
   SEARCH
========================= */

.search-section {
  padding: 30px 0 10px;
}

.search-box {
  width: 100%;
  min-height: 58px;
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 0 18px;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
  color: var(--text2);
  transition: border-color 0.25s, box-shadow 0.25s;
}

.search-box:focus-within {
  border-color: var(--border-gold);
  box-shadow: 0 10px 35px rgba(0, 0, 0, 0.2);
}

.search-box input {
  width: 100%;
  border: 0;
  outline: 0;
  background: transparent;
  color: var(--text);
  font-family: var(--font-body);
  font-size: 0.95rem;
}

.search-box input::placeholder {
  color: var(--text2);
}

.clear-search {
  width: 32px;
  height: 32px;
  border: 0;
  border-radius: 50%;
  background: var(--surface2);
  color: var(--text2);
  display: grid;
  place-items: center;
  cursor: pointer;
}

/* =========================
   INLINE RESULTS
========================= */

.inline-results,
.interest-results {
  margin-top: 18px;
  padding: 24px;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
}

.result-header {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  gap: 20px;
  margin-bottom: 20px;
}

.result-header h3 {
  margin-top: 6px;
  font-family: var(--font-display);
  font-size: 1.45rem;
  color: var(--text);
}

.result-query {
  color: var(--accent);
  font-size: 0.85rem;
}

.meetup-results-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 14px;
}

.meetup-result-card {
  padding: 20px;
  background: var(--bg);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
  transition: transform 0.25s, border-color 0.25s;
}

.meetup-result-card:hover {
  transform: translateY(-3px);
  border-color: var(--border-card);
}

.result-card-top,
.upcoming-small-top {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 10px;
  margin-bottom: 14px;
}

.result-city {
  color: var(--accent);
  font-size: 0.72rem;
  font-weight: 700;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.result-number {
  color: var(--text2);
  font-size: 0.72rem;
}

.meetup-result-card h4 {
  margin-bottom: 12px;
  color: var(--text);
  font-family: var(--font-display);
  font-size: 1.15rem;
}

.meetup-result-card p {
  margin: 14px 0;
  color: var(--text2);
  line-height: 1.6;
  font-size: 0.85rem;
}

.result-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

.result-meta span {
  display: inline-flex;
  align-items: center;
  gap: 5px;
  color: var(--text2);
  font-size: 0.75rem;
}

.result-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
}

.result-tags span {
  padding: 4px 8px;
  background: var(--surface2);
  border: 1px solid var(--border);
  border-radius: 5px;
  color: var(--text2);
  font-size: 0.7rem;
}

.result-link {
  display: inline-block;
  margin-top: 15px;
  color: var(--accent);
  font-size: 0.78rem;
  font-weight: 600;
  text-decoration: none;
}

.result-link:hover {
  color: var(--gold-light);
}

.empty-result,
.city-empty {
  padding: 35px 20px;
  text-align: center;
  color: var(--text2);
}

.empty-result svg,
.city-empty svg {
  margin-bottom: 12px;
  color: var(--accent);
}

.empty-result h4,
.city-empty h3 {
  color: var(--text);
  margin-bottom: 6px;
}

/* =========================
   CITY CARDS
   Existing visual style
========================= */

.city-section {
  padding-top: 55px;
}

.regions-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 16px;
}

.region-card {
  position: relative;
  border-radius: var(--rad2);
  overflow: hidden;
  cursor: pointer;
  aspect-ratio: 3 / 4;
  border: 1px solid var(--border);
  transition:
    border-color 0.3s,
    transform 0.35s,
    box-shadow 0.35s;
}

.region-card:hover {
  border-color: var(--border-gold);
  transform: translateY(-4px);
  box-shadow: 0 24px 64px rgba(0, 0, 0, 0.7);
}

.region-card.featured {
  border-color: var(--border-card);
}

.region-bg {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition:
    transform 0.6s ease,
    filter 0.4s;
  filter: saturate(0.8) brightness(0.75);
}

.region-card:hover .region-bg {
  transform: scale(1.06);
  filter: saturate(1) brightness(0.85);
}

.region-overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(
    to top,
    rgba(5, 6, 5, 0.88) 0%,
    rgba(5, 6, 5, 0.3) 50%,
    transparent 80%
  );
}

.region-gold-tint {
  position: absolute;
  inset: 0;
  background: radial-gradient(
    ellipse 80% 60% at 50% 100%,
    rgba(213, 166, 58, 0.12) 0%,
    transparent 70%
  );
  pointer-events: none;
}

.region-content {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  padding: 20px;
}

.region-top {
  display: flex;
  justify-content: flex-end;
}

.region-badge {
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--accent2);
  background: rgba(213, 166, 58, 0.12);
  backdrop-filter: blur(4px);
  border: 1px solid var(--border-gold);
  padding: 5px 12px;
  border-radius: 100px;
}

.region-bottom {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.region-name {
  font-family: var(--font-display);
  font-size: clamp(20px, 2vw, 26px);
  font-weight: 700;
  letter-spacing: 0.01em;
  color: #ffffff;
  line-height: 1.1;
}

.region-members {
  font-size: 14px;
  color: var(--text2);
}

/* =========================
   INTERESTS
========================= */

.interest-section {
  background: var(--bg2);
}

.interest-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 14px;
}

.interest-card {
  position: relative;
  min-height: 150px;
  padding: 22px;
  text-align: left;
  border: 1px solid var(--border);
  border-radius: var(--rad2);
  background: var(--surface);
  color: var(--text);
  cursor: pointer;
  transition:
    transform 0.25s,
    border-color 0.25s,
    background 0.25s;
}

.interest-card:hover {
  transform: translateY(-3px);
  border-color: var(--border-card);
}

.interest-card.active {
  border-color: var(--accent);
  background: rgba(213, 166, 58, 0.07);
}

.interest-icon {
  width: 42px;
  height: 42px;
  display: grid;
  place-items: center;
  margin-bottom: 18px;
  color: var(--accent);
  background: rgba(213, 166, 58, 0.08);
  border: 1px solid var(--border);
  border-radius: 10px;
}

.interest-card h3 {
  font-family: var(--font-display);
  font-size: 1.05rem;
  margin-bottom: 7px;
}

.interest-card p {
  color: var(--text2);
  font-size: 0.78rem;
  line-height: 1.55;
  max-width: 230px;
}

.interest-arrow {
  position: absolute;
  right: 18px;
  top: 18px;
  color: var(--text2);
}

/* =========================
   UPCOMING
========================= */

.upcoming-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 18px;
}

.upcoming-card-small {
  padding: 26px;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
}

.upcoming-badge {
  display: inline-flex;
  align-items: center;
  gap: 7px;
  color: var(--accent);
  font-size: 0.72rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.1em;
}

.upcoming-badge span {
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: var(--accent);
}

.upcoming-card-small h3 {
  margin-bottom: 16px;
  font-family: var(--font-display);
  font-size: 1.5rem;
}

.upcoming-details {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  margin-bottom: 15px;
}

.upcoming-details div {
  display: flex;
  align-items: center;
  gap: 6px;
  color: var(--text2);
  font-size: 0.78rem;
}

.upcoming-card-small > p {
  color: var(--text2);
  line-height: 1.65;
  font-size: 0.85rem;
}

/* =========================
   SUGGEST
========================= */

.suggest-section {
  padding-top: 30px;
}

.suggest-box {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 30px;
  padding: 42px;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
}

.suggest-content h2 {
  margin: 7px 0 10px;
  font-family: var(--font-display);
  font-size: 2rem;
}

.suggest-content p {
  max-width: 650px;
  color: var(--text2);
  line-height: 1.65;
}

.submit-btn {
  flex-shrink: 0;
  display: inline-block;
  padding: 12px 24px;
  border: 0;
  border-radius: 8px;
  background: var(--accent);
  color: #000;
  font-weight: 700;
  font-size: 0.9rem;
  font-family: var(--font-body);
  text-decoration: none;
  cursor: pointer;
  transition: all 0.25s;
}

.submit-btn:hover {
  background: var(--gold-light);
  transform: translateY(-2px);
}

.suggest-form {
  margin-top: 14px;
  padding: 28px;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
}

.suggest-form-header {
  display: flex;
  justify-content: space-between;
  gap: 20px;
  margin-bottom: 25px;
}

.suggest-form-header h3 {
  margin-top: 6px;
  font-family: var(--font-display);
  font-size: 1.4rem;
}

.form-close,
.close-results {
  border: 0;
  background: var(--surface2);
  color: var(--text2);
  cursor: pointer;
  border-radius: 8px;
}

.form-close {
  width: 36px;
  height: 36px;
  display: grid;
  place-items: center;
}

.form-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 18px;
}

.form-grid label {
  display: flex;
  flex-direction: column;
  gap: 7px;
}

.form-grid label.full {
  grid-column: 1 / -1;
}

.form-grid label > span {
  color: var(--text2);
  font-size: 0.75rem;
}

.form-grid input,
.form-grid textarea {
  width: 100%;
  border: 1px solid var(--border);
  outline: 0;
  border-radius: 8px;
  padding: 12px 14px;
  background: var(--bg);
  color: var(--text);
  font-family: var(--font-body);
  resize: vertical;
}

.form-grid input:focus,
.form-grid textarea:focus {
  border-color: var(--border-gold);
}

.suggest-form-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 20px;
  margin-top: 22px;
  padding-top: 20px;
  border-top: 1px solid var(--border);
}

.suggest-form-footer p {
  color: var(--text2);
  font-size: 0.78rem;
}

/* =========================
   ARCHIVE
========================= */

.archive-section {
  padding-top: 20px;
}

.archive-toggle {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  padding: 22px 24px;
  border: 1px solid var(--border);
  border-radius: var(--rad2);
  background: var(--surface);
  color: var(--text);
  text-align: left;
  cursor: pointer;
}

.archive-toggle strong {
  display: block;
  margin-top: 5px;
  font-family: var(--font-display);
  font-size: 1.15rem;
}

.archive-arrow {
  width: 34px;
  height: 34px;
  display: grid;
  place-items: center;
  border-radius: 50%;
  background: var(--surface2);
  color: var(--accent);
  transition: transform 0.25s;
}

.archive-arrow.open {
  transform: rotate(180deg);
}

.archive-results {
  margin-top: 14px;
  padding: 24px;
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--rad2);
}

.archive-filter-row {
  display: flex;
  justify-content: space-between;
  gap: 20px;
  margin-bottom: 20px;
  color: var(--text2);
  font-size: 0.8rem;
}

.close-results {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 7px 12px;
}

/* =========================
   RESPONSIVE
========================= */

@media (max-width: 1100px) {
  .regions-grid {
    grid-template-columns: repeat(3, 1fr);
  }

  .interest-grid {
    grid-template-columns: repeat(2, 1fr);
  }

  .meetup-results-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 800px) {
  .meetups-intro {
    padding-top: 90px;
  }

  .regions-grid {
    grid-template-columns: repeat(2, 1fr);
  }

  .upcoming-grid {
    grid-template-columns: 1fr;
  }

  .suggest-box {
    align-items: flex-start;
    flex-direction: column;
    padding: 28px;
  }

  .result-header {
    align-items: flex-start;
    flex-direction: column;
  }
}

@media (max-width: 600px) {
  .regions-grid,
  .interest-grid,
  .meetup-results-grid {
    grid-template-columns: 1fr;
  }

  .form-grid {
    grid-template-columns: 1fr;
  }

  .form-grid label.full {
    grid-column: auto;
  }

  .suggest-form-footer {
    align-items: flex-start;
    flex-direction: column;
  }

  .archive-filter-row {
    align-items: flex-start;
    flex-direction: column;
  }

  .inline-results,
  .interest-results,
  .archive-results {
    padding: 16px;
  }
}
</style>

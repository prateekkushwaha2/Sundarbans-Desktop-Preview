<template>
  <footer class="site-footer">
    <div class="footer-orbit" aria-hidden="true">
      <span></span><span></span><span></span>
    </div>

    <div class="footer-container">
      <div class="footer-top">
        <div class="footer-brand">
          <div class="brand-lockup">
            <div class="brand-mark">
              <span>S</span>
            </div>
            <div>
              <strong>SUNDARBANS</strong>
              <small>IIT Madras BS Degree</small>
            </div>
          </div>

          <p>
            A student community built around people, ideas, learning and
            actually showing up.
          </p>

          <router-link to="/community" class="footer-cta">
            Join the community
            <ArrowUpRight :size="16" />
          </router-link>
        </div>

        <div class="footer-links">
          <div class="footer-column">
            <span class="footer-label">Explore</span>
            <router-link to="/about">About Us</router-link>
            <router-link to="/events">Events</router-link>
            <router-link to="/meetups">Meetups</router-link>
            <router-link to="/community">Community</router-link>
          </div>

          <div class="footer-column">
            <span class="footer-label">Learn</span>
            <router-link to="/study">Study Corner</router-link>
            <router-link to="/teams">Our Teams</router-link>
            <router-link to="/lounge">Members Lounge</router-link>
            <router-link to="/contact">Contact</router-link>
          </div>

          <div class="footer-column">
            <span class="footer-label">Find us</span>
            <a
              v-for="social in socials"
              :key="social.label"
              :href="social.href"
              target="_blank"
              rel="noopener noreferrer"
            >
              {{ social.label }}
              <ArrowUpRight :size="13" />
            </a>
          </div>
        </div>
      </div>

      <div class="footer-meetup-strip">
        <div>
          <span>STILL HERE?</span>
          <strong>Find your next meetup.</strong>
        </div>
        <router-link to="/meetups">
          Explore meetups
          <ArrowRight :size="16" />
        </router-link>
      </div>

      <div class="footer-bottom">
        <div class="footer-affiliation">
          <img
            src="https://res.cloudinary.com/l59gy0g2/image/upload/f_auto,q_auto:good,w_180/v1785911353/sundarbans/src/assets/IITM-Logo.png"
            alt=""
            width="36"
            height="36"
            loading="lazy"
            decoding="async"
          />
          <span>IIT Madras Affiliated</span>
        </div>

        <div class="footer-legal">
          <span>© {{ year }} Sundarbans House WebOps Team</span>
          <span class="footer-dot">•</span>
          <router-link to="/contact">Contact</router-link>
          <router-link to="/about">About</router-link>
        </div>
      </div>
    </div>
  </footer>
</template>

<script setup>
import { ArrowRight, ArrowUpRight } from 'lucide-vue-next';

const year = new Date().getFullYear();

const socials = [
  {
    label: 'LinkedIn',
    href: 'https://www.linkedin.com/company/sundarbans-iitm/',
  },
  {
    label: 'Instagram',
    href: 'https://www.instagram.com/sundarbansiitm/',
  },
  {
    label: 'YouTube',
    href: 'https://www.youtube.com/@sundarbansiitm',
  },
];
</script>

<style scoped>
.site-footer {
  position: relative;
  overflow: hidden;
  background: #111;
  color: #f5f0e6;
  border-top: 2px solid #111;
}

.footer-container {
  position: relative;
  z-index: 2;
  width: min(1180px, calc(100% - 40px));
  margin: 0 auto;
}

.footer-orbit {
  position: absolute;
  width: 480px;
  height: 480px;
  right: -180px;
  top: -250px;
  border: 1px solid rgba(245, 240, 230, 0.1);
  border-radius: 50%;
}

.footer-orbit::before,
.footer-orbit::after {
  position: absolute;
  content: "";
  border: 1px solid rgba(245, 240, 230, 0.08);
  border-radius: 50%;
}

.footer-orbit::before {
  inset: 55px;
}

.footer-orbit::after {
  inset: 115px;
}

.footer-orbit span {
  position: absolute;
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: #f6c945;
}

.footer-orbit span:nth-child(1) {
  left: 70px;
  bottom: 40px;
}

.footer-orbit span:nth-child(2) {
  right: 75px;
  top: 80px;
  background: #ff7c91;
}

.footer-orbit span:nth-child(3) {
  left: 205px;
  top: 28px;
  background: #71b8ff;
}

.footer-top {
  display: grid;
  grid-template-columns: 1.05fr 1fr;
  gap: 100px;
  padding: 80px 0 65px;
}

.brand-lockup {
  display: flex;
  align-items: center;
  gap: 13px;
}

.brand-mark {
  width: 48px;
  height: 48px;
  display: grid;
  place-items: center;
  border: 2px solid #f5f0e6;
  border-radius: 13px;
  background: #f6c945;
  color: #111;
  transform: rotate(-4deg);
  box-shadow: 4px 4px 0 #f5f0e6;
}

.brand-mark span {
  font-family: Georgia, serif;
  font-size: 1.65rem;
  font-weight: 900;
}

.brand-lockup strong {
  display: block;
  font-size: 1rem;
  letter-spacing: 0.12em;
}

.brand-lockup small {
  display: block;
  margin-top: 3px;
  color: rgba(245, 240, 230, 0.5);
  font-size: 0.66rem;
}

.footer-brand > p {
  max-width: 430px;
  margin: 25px 0;
  color: rgba(245, 240, 230, 0.62);
  line-height: 1.7;
  font-size: 0.86rem;
}

.footer-cta,
.footer-meetup-strip a {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  text-decoration: none;
  font-weight: 800;
}

.footer-cta {
  color: #111;
  padding: 11px 15px;
  border-radius: 8px;
  background: #f6c945;
  transition: transform 0.2s;
}

.footer-cta:hover,
.footer-meetup-strip a:hover {
  transform: translateY(-2px);
}

.footer-links {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 35px;
}

.footer-column {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 12px;
}

.footer-label {
  margin-bottom: 7px;
  color: rgba(245, 240, 230, 0.38);
  font-size: 0.62rem;
  font-weight: 900;
  letter-spacing: 0.14em;
  text-transform: uppercase;
}

.footer-column a {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  color: rgba(245, 240, 230, 0.8);
  font-size: 0.8rem;
  text-decoration: none;
  transition: color 0.2s, transform 0.2s;
}

.footer-column a:hover {
  color: #f6c945;
  transform: translateX(3px);
}

.footer-meetup-strip {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 25px;
  padding: 21px 0;
  border-top: 1px solid rgba(245, 240, 230, 0.13);
  border-bottom: 1px solid rgba(245, 240, 230, 0.13);
}

.footer-meetup-strip span {
  display: block;
  margin-bottom: 4px;
  color: rgba(245, 240, 230, 0.38);
  font-size: 0.58rem;
  font-weight: 900;
  letter-spacing: 0.14em;
}

.footer-meetup-strip strong {
  font-family: var(--font-display);
  font-size: 1.25rem;
}

.footer-meetup-strip a {
  padding: 9px 13px;
  border: 1px solid rgba(245, 240, 230, 0.3);
  border-radius: 8px;
  color: #f5f0e6;
  font-size: 0.72rem;
}

.footer-bottom {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  padding: 24px 0;
}

.footer-affiliation,
.footer-legal {
  display: flex;
  align-items: center;
  gap: 9px;
}

.footer-affiliation {
  color: rgba(245, 240, 230, 0.58);
  font-size: 0.7rem;
}

.footer-affiliation img {
  width: 36px;
  height: 36px;
  object-fit: contain;
}

.footer-legal {
  flex-wrap: wrap;
  justify-content: flex-end;
  color: rgba(245, 240, 230, 0.4);
  font-size: 0.65rem;
}

.footer-legal a {
  color: rgba(245, 240, 230, 0.65);
  text-decoration: none;
}

.footer-legal a:hover {
  color: #f6c945;
}

.footer-dot {
  opacity: 0.3;
}

@media (max-width: 800px) {
  .footer-top {
    grid-template-columns: 1fr;
    gap: 45px;
    padding: 60px 0 45px;
  }
}

@media (max-width: 560px) {
  .footer-container {
    width: min(100% - 24px, 1180px);
  }

  .footer-links {
    grid-template-columns: repeat(2, 1fr);
  }

  .footer-meetup-strip,
  .footer-bottom {
    align-items: flex-start;
    flex-direction: column;
  }

  .footer-legal {
    justify-content: flex-start;
  }
}
</style>

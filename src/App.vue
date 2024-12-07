<script setup>
import { ref, onMounted } from "vue";
import "leaflet/dist/leaflet.css";
import L from "leaflet";
import axios from "axios";
import IconArrow from "./assets/images/icon-arrow.svg";
import IconLocation from "./assets/images/icon-location.svg";

const geoApiKey = import.meta.env.VITE_API_KEY;
const ipAddress = ref(null);
const ip = ref(null);

const location = ref(null);
const timezone = ref(null);
const isp = ref(null);
const lat = ref(null);
const lon = ref(null);

const center = [38.8937, -77.0971];
const mapDiv = ref(null);
const iconLocation = L.icon({
  iconUrl: IconLocation,
  iconSize: [50, 64],
  iconAnchor: [22, 22],
});

const getIpAddress = async () => {
  try {
    const response = await axios.get("https://api.ipify.org?format=json");
    ipAddress.value = response.data.ip;
    await getLocationDetails();
  } catch (error) {
    console.error("Error IP", error);
  }
};

const getLocationDetails = async () => {
  try {
    const url = `https://geo.ipify.org/api/v2/country,city?apiKey=${geoApiKey}&ipAddress=${ipAddress.value}`;
    const response = await axios.get(url);
    ip.value = response.data.ip;
    location.value = `${response.data.location.city}, ${response.data.location.region}`;
    timezone.value = response.data.location.timezone;
    isp.value = response.data.isp;
    lat.value = response.data.location.lat;
    lon.value = response.data.location.lng;

    L.marker([lat.value, lon.value], { icon: iconLocation }).addTo(
      mapDiv.value
    );
    mapDiv.value.setView([lat.value, lon.value]);
    mapDiv.value.setZoom(20);
  } catch (error) {
    console.error("Error location details", error);
  }
};

const createMap = () => {
  mapDiv.value = L.map("map").setView(center, 13);
  L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
    attribution:
      '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>',
    maxZoom: 19,
  }).addTo(mapDiv.value);
};

onMounted(() => {
  getIpAddress();
  createMap();
});
</script>

<template>
  <section class="info-section">
    <div class="container">
      <h2>Apa itu IP Address Tracker?</h2>
      <p>
        IP Address Tracker adalah alat yang digunakan untuk melacak dan mendapatkan informasi terkait alamat IP tertentu. Alamat IP (Internet Protocol) adalah serangkaian angka yang digunakan untuk mengidentifikasi perangkat di jaringan. Dengan menggunakan IP Address Tracker, Anda dapat mengetahui lokasi geografis, penyedia layanan internet (ISP), dan informasi lainnya yang terkait dengan alamat IP tersebut.
      </p>
      <p>
        Alat ini sangat berguna untuk berbagai keperluan, seperti:
      </p>
      <ul>
        <li>Mengetahui lokasi pengguna yang mengakses situs web Anda.</li>
        <li>Memecahkan masalah jaringan dengan melacak alamat IP yang bermasalah.</li>
        <li>Meningkatkan keamanan dengan memantau aktivitas IP yang mencurigakan.</li>
        <li>Mengetahui informasi ISP untuk keperluan analisis dan pengembangan.</li>
      </ul>
      <h3>Versi Alamat IP</h3>
      <ul>
        <li>
          <strong>IP versi 4 (IPv4):</strong> Versi alamat IP yang paling umum, terdiri dari 32 bit.
        </li>
        <li>
          <strong>IP versi 6 (IPv6):</strong> Versi terbaru yang terdiri dari 128 bit, dirancang untuk menggantikan IPv4.
        </li>
      </ul>

      <h3>Jenis-Jenis Alamat IP</h3>
      <ul>
        <li>
          <strong>Alamat IP Publik:</strong> Digunakan untuk berkomunikasi di luar jaringan.
        </li>
        <li>
          <strong>Alamat IP Privat:</strong> Digunakan di dalam jaringan lokal.
        </li>
      </ul>

      <h3>IP Dinamis vs IP Statis</h3>
      <p>
        IP dinamis diberikan sementara, sedangkan IP statis tetap tidak berubah hingga diubah oleh pengguna.
      </p>

      <h3>Pentingnya Alamat IP</h3>
      <p>
        Alamat IP adalah pengidentifikasi unik di jaringan yang memungkinkan Anda mengirim dan menerima informasi di
        jaringan. Tanpa alamat IP, Anda tidak dapat mengakses internet.
      </p>

      <h3>Apakah Alamat IP Anda Bisa Tetap Tidak Berubah?</h3>
      <p>
        Alamat IP Anda dapat berubah tergantung pada lokasi Anda dan jaringan yang Anda gunakan.
      </p>

    </div>
  </section>
  <header class="header">
    <div class="container center">
      <h1 class="header-title">IP Address Tracker Kelompok 10</h1>
      <form @submit.prevent="getLocationDetails" class="header-form">
        <input
          v-model="ipAddress"
          type="text"
          id="search"
          placeholder="Search for any IP address or domain"
        />
        <button type="submit" aria-label="Search">
          <img :src="IconArrow" alt="" srcset="" />
        </button>
      </form>
      <div class="header-info">
        <div>
          <h4>IP Address</h4>
          <p class="info-text">
            <span class="address"> {{ ip }}</span>
          </p>
        </div>
        <div>
          <h4>Location</h4>
          <p class="info-text">
            <span class="location"> {{ location }}</span>
          </p>
        </div>
        <div>
          <h4>Timezone</h4>
          <p class="info-text">
            <span class="timezone"> UTC {{ timezone }} </span>
          </p>
        </div>
        <div>
          <h4>ISP</h4>
          <p class="info-text">
            <span class="isp"> {{ isp }}</span>
          </p>
        </div>
      </div>
    </div>
  </header>
    <div class="map-container">
      <div id="map" class="map"></div>
    </div>

</template>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Rubik:wght@300;400;500;600;700&display=swap");

$very-dark-gray: hsl(0, 0%, 17%);
$dark-gray: hsl(0, 0%, 59%);
$font-size-body-copy: 18px;

* {
  margin: 0;
  padding: 0;
}

body {
  font-family: "Rubik", sans-serif;
  line-height: 1.5;
  color: $dark-gray;
}

button {
  cursor: pointer;
}

h1 {
  font-size: 1.6rem;
}

.header {
  z-index: 999;
  text-align: center;
  background-image: url("assets/images/pattern-bg-desktop.png");
  background-size: cover;

  .container {
    margin: 0 auto;
    width: 100%;
    max-width: 1440px;
    padding-bottom: 6rem;
    position: relative;
    flex-direction: column;
    align-items: center;
    gap: 1rem;
  }

  .center {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .header-title {
    margin-top: 1rem;
    margin-bottom: 1rem;
    font-weight: 500;
    color: white;
  }

  .header-form {
    display: flex;
    width: 100%;
    max-width: 500px;

    input,
    button {
      border: 0px;
      padding: 14px 20px;
    }

    input {
      width: 100%;
      font-size: 1rem;
      border-top-left-radius: 11px;
      border-bottom-left-radius: 11px;
      color: $dark-gray;

      &:focus {
        cursor: pointer;
      }
    }

    button {
      background-color: #000;
      border-top-right-radius: 11px;
      border-bottom-right-radius: 11px;
    }
  }

  .header-info {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-left: 16px;
    margin-right: 16px;
    position: absolute;
    bottom: -30%;
    z-index: 999;
    text-align: left;
    background-color: white;
    border-radius: 11px;
    padding: 2rem 1rem;

    div {
      padding: 0 4rem 0 1rem;
      h4 {
        color: $dark-gray;
        text-transform: uppercase;
        font-size: 12px;
        letter-spacing: 2px;
      }

      p:first-child {
        font-size: small;
        color: #333;
      }

      &:not(:last-child) {
        border-right: 1px solid #c8c4c4;
      }
    }

    .info-text {
      font-size: x-large;
      font-weight: 500;
      color: #000;
      margin-top: 6px;
    }
  }
}

@media (max-width: 770px) {
  .header {
    background-image: url("assets/images/pattern-bg-mobile.png");
    background-size: cover;

    .header-form {
      width: 90%;
    }

    .header-info {
      bottom: -75%;
      padding: 12px 0;
      align-items: center;
      text-align: center;
      flex-direction: column;
      position: absolute;
      width: 90%;

      div {
        padding: 6px 2px;

        .info-text {
          font-size: 16px;
          color: #000;
          margin-top: 2px;
        }
      }

      div:not(:last-child) {
        border-right: none;
      }
    }
  }
}

.map-container {
  height: 100vh;

  .map {
    height: 100%;
  }
}

.info-section {
  background-color: #f9f9f9; /* Warna latar belakang yang lembut */
  padding: 40px 20px; /* Ruang di dalam section */
  border-radius: 8px; /* Sudut yang membulat */
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1); /* Bayangan halus */
}

.info-section .container {
  max-width: 800px; /* Lebar maksimum untuk konten */
  margin: 0 auto; /* Pusatkan konten */
}

.info-section h2 {
  font-size: 24px; /* Ukuran font untuk judul */
  color: #333; /* Warna teks judul */
  margin-bottom: 20px; /* Jarak bawah judul */
}

.info-section p {
  font-size: 16px; /* Ukuran font untuk paragraf */
  color: #555; /* Warna teks paragraf */
  line-height: 1.6; /* Jarak antar baris */
  margin-bottom: 15px; /* Jarak bawah paragraf */
}

.info-section ul {
  list-style-type: disc; /* Tipe daftar */
  padding-left: 20px; /* Ruang di sebelah kiri untuk daftar */
}

.info-section li {
  font-size: 16px; /* Ukuran font untuk item daftar */
  color: #555; /* Warna teks item daftar */
  margin-bottom: 10px; /* Jarak bawah item daftar */
}
</style>

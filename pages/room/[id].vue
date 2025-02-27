<script setup>
const router = useRouter();
const route = useRoute();

const { id } = route.params;

const { data: roomObject } = await useFetch(`/rooms/${id}`, {
  baseURL: "https://nuxr3.zeabur.app/api/v1",
  transform: (response) => {
    const { result } = response;
    return result;
  },
  onResponseError({ response }) {
    const { message } = response._data;
    console.error("Error:", message);
    router.push("/");
  },
});

// 使用 useSeoMeta  將 roomObject 的資訊寫入 SEO Meta
/* 請撰寫 useSeoMeta({ }) 渲染出下方的 HTML 結構，並將 {{ }}  改成使用 roomObject 物件的資料。
<title> Freyja | {{ 房型名稱 }}</title>
<meta name="description" content="{{ 房型描述 }}">
<meta property="og:title" content="Freyja | {{ 房型名稱 }} ">
<meta property="og:description" content="{{ 房型描述 }}">
<meta property="og:image" content="{{房型主圖}}">
<meta property="og:url" content="https://freyja.travel.com.tw/room/{房型 id }">
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Freyja | {{ 房型名稱 }}">
<meta name="twitter:description" content="{{ 房型描述 }}">
<meta name="twitter:image" content="{{房型主圖}}">
*/


const title = () => `Freyja | ${ roomObject.value.name}`;
const description = () => roomObject.value.description;
const ogTitle = () => `Freyja | ${ roomObject.value.name} `;
const ogdescription = () =>  roomObject.value.description;
const ogImage = () => roomObject.value.imageUrl;
const ogUrl = () => `https://freyja.travel.com.tw/room/ ${ roomObject.value._id } `;
const twitterTitle = () => `Freyja | ${ roomObject.value.name} `;
const twitterDescription = () => roomObject.value.description;
const twitterImage = () => roomObject.value.imageUrl ;


useSeoMeta({
  title,
  description,
  ogTitle,
  ogdescription,
  ogImage,
  ogUrl,
  twitterCard : `summary_large_image`,
  twitterTitle,
  twitterDescription,
  twitterImage,
});


const isProvide = function (isProvideBoolean = false) {
  return isProvideBoolean ? "提供" : "未提供";
};
</script>

<template>
  <h2>房型詳細頁面</h2>
  <div class="container">
    <button @click="router.go(-1)">回上一頁</button>
    <div class="row justify-content-center">
      <div class="col-lg-6">
        <div class="room-page">
          <div class="room-header">
            <h1 class="room-name">{{ roomObject.name }}</h1>
            <p class="room-description">
              {{ roomObject.description }}
            </p>
          </div>

          <div class="room-gallery">
            <img
              :src="roomObject.imageUrl"
              :alt="roomObject.name"
              class="room-main-image"
            />
            <ul class="room-image-list">
              <li v-for="(imageUrl, index) in roomObject.imageUrlList">
                <img
                  :src="imageUrl"
                  :alt="`${roomObject.name}圖片${index + 1}`"
                />
              </li>
            </ul>
          </div>

          <div class="room-info">
            <div class="info-block">
              <h2>房間資訊</h2>
              <p>面積: {{ roomObject.areaInfo }}</p>
              <p>床型: {{ roomObject.bedInfo }}</p>
              <p>最多容納人數: {{ roomObject.maxPeople }}</p>
              <p>價格: {{ roomObject.price }}</p>
            </div>

            <div class="info-block">
              <h2>房間配置</h2>
              <ul>
                <li v-for="layout in roomObject.layoutInfo" :key="layout.title">
                  {{ layout.title }}: {{ isProvide(layout.isProvide) }}
                </li>
              </ul>
            </div>

            <div class="info-block">
              <h2>房內設施</h2>
              <ul>
                <li
                  v-for="facility in roomObject.facilityInfo"
                  :key="facility.title"
                >
                  {{ facility.title }}: {{ isProvide(facility.isProvide) }}
                </li>
              </ul>
            </div>

            <div class="info-block">
              <h2>客房備品</h2>
              <ul>
                <li
                  v-for="amenity in roomObject.amenityInfo"
                  :key="amenity.title"
                >
                  {{ amenity.title }}: {{ isProvide(amenity.isProvide) }}
                </li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.room-page {
  max-width: 1200px;
  margin: 0 auto;
  background-color: #fff;
  padding: 20px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  border-radius: 10px;
}

.room-header {
  text-align: center;
  margin-bottom: 30px;
}

.room-name {
  font-size: 2rem;
  color: #333;
}

.room-description {
  font-size: 1rem;
  color: #666;
}

.room-gallery {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 30px;
}

.room-main-image {
  width: 100%;
  max-width: 600px;
  height: auto;
  border-radius: 10px;
  margin-bottom: 20px;
}

.room-image-list {
  display: flex;
  justify-content: center;
  gap: 10px;
  padding: 0;
  list-style: none;
}

.room-image-list img {
  width: 100px;
  height: auto;
  border-radius: 5px;
  cursor: pointer;
  transition: transform 0.3s;
}

.room-image-list img:hover {
  transform: scale(1.1);
}

.room-info {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.info-block {
  background-color: #f0f0f0;
  padding: 15px;
  border-radius: 10px;
}

.info-block h2 {
  font-size: 1.5rem;
  margin-bottom: 10px;
  color: #444;
}

.info-block p,
.info-block ul {
  font-size: 1rem;
  color: #555;
}

.info-block ul {
  list-style: none;
  padding-left: 0;
}

.info-block ul li {
  margin-bottom: 5px;
}
</style>

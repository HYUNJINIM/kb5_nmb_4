<template>
    <!-- 네비게이션 바를 정의합니다. Bootstrap 클래스들을 사용합니다. -->
    <div class="container">
        <nav class="sidebar ps-3 pt-2">
            <div class="row g-0">
                <span class="navbar-brand ps-2 mt-5"><router-link class="nav-link" to="/"><img src="../assets/logo.png" alt="logo"/></router-link></span>
                <div class="col-md-4 mt-2">
                    <img
                        src="../assets/profile.png"
                        class="profile-card rounded-circle me-3" width="100%"
                        alt="..."/>
                </div>
                <div class="col-md-8">
                    <div class="card-body">
                        <p class="card-text">
                            <div>                   
                                <div class="fw-bold">{{ profileStore.profile.nickname}} 님</div>
                                <div class="text-muted">@ {{ profileStore.profile.account_id}}</div> 
                            </div>
                        </p>
                    </div>
                </div>
            </div>
            <!-- 네비게이션 메뉴를 정의합니다. 'collapse' 클래스를 사용하여 기본적으로 숨겨지도록 설정합니다. -->
            <div class="mt-3">
                <!-- 네비게이션 메뉴 항목을 정의합니다. -->
                <ul class="navbar-nav flex-column">
                    <li class="nav-item">
                        <!-- Vue Router를 사용하여 페이지 간의 네비게이션을 처리합니다. -->
                        <router-link class="nav-link" to="/">
                            <font-awesome-icon :icon="['fas', 'house']" /> Home
                        </router-link>
                    </li>
                    <li class="nav-item">
                        <router-link class="nav-link" to="/write">
                            <font-awesome-icon
                                :icon="['fas', 'calendar-plus']"
                            />
                            추가 / 수정
                        </router-link>
                    </li>
                    <li class="nav-item">
                        <router-link class="nav-link" to="/summary">
                            <font-awesome-icon :icon="['fas', 'chart-pie']" />
                            소비 요약
                        </router-link>
                    </li>
                    <li class="nav-item">
                        <router-link class="nav-link" to="/profile">
                            <font-awesome-icon :icon="['fas', 'gear']" /> 마이
                            페이지
                        </router-link>
                    </li>
                </ul>
            </div>
            <div>
            <AddButton
                            :categories="categories"
                            @data-added="fetchContent"
            /></div>
        </nav>
    </div>
</template>

<script setup lang="ts">
// import { defineProps } from "vue";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import axios from "axios";
import { ref, onMounted } from 'vue';
import { useMainStore } from "@/stores/content.js";
import { useRoute } from "vue-router";
import { useContentStore } from "@/stores/content.js";

import AddButton from "../components/AddButton.vue"; // Import AddButton component

const currentRoute = useRoute();
const profileStore = useMainStore();
const contentStore = useContentStore();
const currentDate = ref(new Date());

const categories = ref([]);

const BASEURI = "http://localhost:3000";
const fetchCategories = async () => {
    try {
        const response = await axios.get(`${BASEURI}/category`);
        if (response.status === 200) {
            categories.value = response.data;
        } else {
            console.error("Failed to fetch categories");
        }
    } catch (error) {
        console.error("Error fetching categories:", error);
    }
};
const fetchContent = async () => {
    try {
        const response = await axios.get(`${BASEURI}/content`);
        if (response.status === 200) {
            contentStore.state.contentList = response.data || [];
        } else {
            console.error("Failed to fetch data");
            alert("데이터 조회 실패");
        }
    } catch (error) {
        console.error("Error fetching data:", error);
        alert("에러 발생:" + error);
    } 
};
onMounted(async() => {
      await profileStore.fetchProfile();
    });

// const props = defineProps({
//     profileImage: String,
//     profileNickname: String,
//     profileEmail: String,
// });

</script>

<style scoped>
.sidebar {
    background-color: rgb(95, 103, 121);
    width: 250px; /* 네비게이션 바의 너비를 설정합니다. */
    margin: 0; /* 모든 외부 여백을 제거합니다. */
    padding: 0; /* 모든 내부 여백을 제거합니다. */
    position: fixed; /* 화면에 고정합니다. */
    top: 0; /* 상단 여백을 제거합니다. */
    left: 0; /* 왼쪽 여백을 제거합니다. */
    height: 100%; /* 화면 높이에 맞춥니다. */
}
.profile-card {
    width: 100%;
    background-color: #f8f9fa; /* 카드 배경색 */
    border-radius: 15px; /* 카드의 둥근 모서리 */
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); /* 카드의 그림자 */
    overflow: hidden; /* 둥근 모서리 외부의 내용이 잘리도록 설정 */
}
.card-text{
    font-size: 11pt;
    margin-left: 10pt;
    margin-top: 20pt;
}

</style>

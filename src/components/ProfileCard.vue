<template>
  <div class="profile-card">
    <div class="profile-card-avatar">
      <img
        :src="`https://ui-avatars.com/api/?background=random&size=128&rounded=true&name=${profile.name}`"
      />
    </div>
    <div class="profile-card-title">
      {{ profile.name }}
    </div>
    <div class="profile-card-subtitle">
      <span
        style="padding-right: 5px"
        v-for="(i, index) in profile.specialisation"
        :key="index"
        >#{{ i.name }}
      </span>
    </div>
    <div class="profile-card-email">
      {{ profile.email }}
    </div>
    <div class="profile-card-description">
      {{ profile.description }}
    </div>
    <div class="profile-card-leave-comment">
      <textarea
        v-model="comment"
        @keydown.enter.shift.exact="keydownDetect(profile.id)"
        placeholder="Leave a comment (send by Shift+Enter)"
      ></textarea>
    </div>
    <div class="profile-card-reactive">
      <span @click="Like(profile.id)"
        ><i class="fa fa-thumbs-up"></i> {{ profile.likeCount }}</span
      >
      <span @click="DisLike(profile.id)"
        ><i class="fa fa-thumbs-down"></i>
      </span>
      <span><i class="fa fa-comment"></i> {{ profile.commentCount }}</span>
    </div>
  </div>
</template>
<script>
export default {
  name: "ProfileCard",
  props: ["profile"],
  data: () => ({
    comment: "",
  }),
  methods: {
    keydownDetect(id) {
      let vm = this;
      var getSavedItems = window.localStorage.getItem("profiles");
      if (getSavedItems != null) {
        var profiles = JSON.parse(getSavedItems);
        profiles.forEach((el) => {
          if (el.id == id) {
            el.commentCount = el.commentCount + 1;
            vm.profile.commentCount = el.commentCount;
          }
        });
        window.localStorage.setItem("profiles", JSON.stringify(profiles));
        vm.comment = null;
      }
    },
    arrayToString(array) {
      return array.join(",");
    },
    Like(id) {
      let vm = this;
      var getSavedItems = window.localStorage.getItem("profiles");
      if (getSavedItems != null) {
        var profiles = JSON.parse(getSavedItems);
        profiles.forEach((el) => {
          if (el.id == id) {
            if (el.disLikeCount < 0) {
              el.disLikeCount = parseInt(parseInt(el.disLikeCount) + 1);
            }
            el.likeCount = parseInt(parseInt(el.likeCount) + 1);
            vm.profile.likeCount = el.likeCount;
            vm.profile.disLikeCount = el.disLikeCount;
            console.warn(profiles);
          }
        });
        window.localStorage.setItem("profiles", JSON.stringify(profiles));
      }
    },
    DisLike(id) {
      let vm = this;
      var getSavedItems = window.localStorage.getItem("profiles");
      if (getSavedItems != null) {
        var profiles = JSON.parse(getSavedItems);
        profiles.forEach((el) => {
          if (el.id == id) {
            el.likeCount = parseInt(parseInt(el.likeCount) - 2);
            vm.profile.likeCount = el.likeCount;
            vm.profile.disLikeCount = el.disLikeCount + 2;
          }
        });
        window.localStorage.setItem("profiles", JSON.stringify(profiles));
      }
    },
  },
};
</script>

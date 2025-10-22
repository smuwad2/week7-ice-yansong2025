<script>
import axios from "axios";

export default {
    data() {
        return {
            moods: ["Happy", "Sad", "Angry"],
            posts: [],
            entry: "",
            mood: "",
            showEditPost: false,
            editPostId: "",
            outputMsg: ""
        };
    },
    computed: {
        baseUrl() {
            if (window.location.hostname === "localhost") return "http://localhost:3000";
            const codespace_host = window.location.hostname.replace("5173", "3000");
            return `https://${codespace_host}`;
        }
    },
    created() {
        this.refreshPosts();
    },
    methods: {
        async refreshPosts() {
            const res = await axios.get(`${this.baseUrl}/posts`);
            this.posts = res.data;
        },

        editPost(id) {
            const p = this.posts.find((x) => x.id === id);
            if (!p) return;
            this.entry = p.entry;
            this.mood = p.mood;
            this.editPostId = id;
            this.showEditPost = true;
            this.outputMsg = "";
        },

        async updatePost() {
            if (!this.editPostId) return;

            // keep current mood if user didn’t change
            const current = this.posts.find(p => p.id === this.editPostId);
            const moodToSave = this.mood || current?.mood || "Neutral";

            // Backend expects: POST /updatePost?id=... with {entry, mood} in body
            await axios.post(
                `${this.baseUrl}/updatePost`,
                { entry: this.entry, mood: moodToSave },
                { params: { id: this.editPostId } }
            );

            // Ensure table shows new value before test asserts
            await this.refreshPosts();
            await this.$nextTick();

            this.showEditPost = false; // close after DOM updated
        }
    }
};
</script>

<template>
    <div id="demo">
        <h2>View Blog Posts</h2>

        <table class="table m-2">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Entry</th>
                    <th>Mood</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="post in posts" :key="post.id">
                    <td>{{ post.id }}</td>
                    <td>{{ post.entry }}</td>
                    <td>{{ post.mood }}</td>
                    <td><button @click="editPost(post.id)">Edit</button></td>
                </tr>
            </tbody>
        </table>

        <div id="editPost" v-if="showEditPost">
            <h3>Edit Post</h3>
            <div id="postContent" class="mx-3">
                <!-- The test looks for a button with name 'Update Post' -->
                <form @submit.prevent="updatePost">
                    <div class="mb-3">
                        <label for="entry" class="form-label">Entry</label>
                        <textarea id="entry" class="form-control" v-model="entry" required></textarea>
                    </div>
                    <div class="mb-3">
                        <label for="mood" class="form-label">Mood</label>
                        <select id="mood" class="form-select" v-model="mood">
                            <option disabled value="">(Keep current mood)</option>
                            <option v-for="m in moods" :key="m" :value="m">{{ m }}</option>
                        </select>
                    </div>
                    <button type="submit" class="btn btn-primary">Update Post</button>
                </form>
            </div>
        </div>
    </div>
</template>
<script>
import axios from "axios";

export default {
    data() {
        return {
            subject: "",
            entry: "",
            selMood: "",
            outputMsg: "",
            mood: ["Happy", "Sad", "Angry"]
        };
    },
    computed: {
        baseUrl() {
            if (window.location.hostname === "localhost") return "http://localhost:3000";
            const codespace_host = window.location.hostname.replace("5173", "3000");
            return `https://${codespace_host}`;
        }
    },
    methods: {
        // Encode spaces as '+' (x-www-form-urlencoded style) to satisfy strict/legacy parsers
        encodeForQuery(v) {
            return encodeURIComponent(v).replace(/%20/g, "+");
        },
        async addPost() {
            try {
                // Build query manually with + for spaces
                const qs =
                    `subject=${this.encodeForQuery(this.subject)}` +
                    `&entry=${this.encodeForQuery(this.entry)}` +
                    `&mood=${this.encodeForQuery(this.selMood)}`;

                await axios.get(`${this.baseUrl}/addPost`, {
                    params: {
                        subject: this.subject,
                        entry: this.entry,
                        mood: this.selMood
                    }
                });

                this.outputMsg = "✅ Post added.";
            } catch (e) {
                console.error(e);
                this.outputMsg = "❌ Failed to add.";
            }
        }
    }
};
</script>

<template>
    <div class="table m-2">
        <h3>Add a New Blog Post</h3>

        Subject:
        <input type="text" size="30" v-model="subject" required />
        <br />

        Entry:
        <br />
        <textarea name="entry" cols="80" rows="5" v-model="entry" required></textarea>
        <br />

        Mood:
        <select v-model="selMood" required>
            <option disabled value="">Select Mood</option>
            <option v-for="mood in moods" :key="mood">{{ mood }}</option>
        </select>

        <br /><br />
        <!-- Ensure no implicit form submit -->
        <button type="button" @click="addPost">Submit New Post</button>
        {{ outputMsg }}

        <hr />
        Click <router-link to="/ViewPosts/">here</router-link> to return to Main Page
    </div>
</template>
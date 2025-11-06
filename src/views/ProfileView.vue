<template>
    <div class="profile-container">
        <!-- Блок отладки -->
        <div v-if="debugMode" class="debug-panel">
            <h3>🔧 Отладка профиля</h3>
            <p><strong>Telegram User:</strong> {{ JSON.stringify(debugUserData) }}</p>
            <p><strong>Статус:</strong> {{ debugStatus }}</p>
            <p><strong>Данные профиля:</strong> {{ JSON.stringify(debugProfileData) }}</p>
            <button @click="toggleDebug" class="debug-btn">Скрыть отладку</button>
        </div>

        <div v-else class="debug-toggle">
            <button @click="toggleDebug" class="debug-btn">Показать отладку</button>
        </div>

        <!-- Основной интерфейс профиля -->
        <div v-if="!debugMode">
            <h2>Профиль</h2>
            <div class="profile-info">
                <p><strong>ID:</strong> {{ user.id || 'Не получен' }}</p>
                <p><strong>Имя:</strong> {{ user.name || 'Не получено' }}</p>
                <p><strong>Выполнено задач:</strong> {{ user.completedTasks }}</p>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'ProfileView',
    data() {
        return {
            user: {
                id: '',
                name: '',
                completedTasks: 0
            },
            // Отладочные данные
            debugMode: true,
            debugUserData: null,
            debugStatus: '',
            debugProfileData: null
        }
    },
    async mounted() {
        await this.fetchProfile()
    },
    methods: {
        toggleDebug() {
            this.debugMode = !this.debugMode
        },
        
        async fetchProfile() {
            try {
                this.debugUserData = window.Telegram?.WebApp?.initDataUnsafe?.user
                this.debugStatus = 'Запрос отправляется...'
                
                const tg_user = window.Telegram.WebApp.initDataUnsafe?.user
                if (!tg_user) {
                    this.debugStatus = 'Ошибка: нет данных пользователя'
                    return
                }
                
                const response = await fetch(`https://studious-halibut-6xxg5r5rwg43rj4r.github.dev/api/main/${tg_user.id}`)
                this.debugStatus = `Статус: ${response.status}`
                
                const data = await response.json()
                this.debugProfileData = data
                
                this.user.id = tg_user.id || 'Не получен'
                this.user.name = tg_user.first_name || 'Не получено'
                this.user.completedTasks = data.completedTasks || 0
                
                this.debugStatus = 'Успешно!'
                
            } catch (error) {
                console.log(error)
                this.debugStatus = `Ошибка: ${error.message}`
            }
        }
    }
}
</script>
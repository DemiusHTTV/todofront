<template>
    <div class="tasks_container">
        <!-- БЛОК ДЛЯ ОТЛАДКИ -->
        <div v-if="debugMode" class="debug-panel">
            <h3>🔧 Отладка</h3>
            <p><strong>Telegram объект:</strong> {{ !!window.Telegram }}</p>
            <p><strong>WebApp объект:</strong> {{ !!window.Telegram?.WebApp }}</p>
            <p><strong>Данные пользователя:</strong> {{ JSON.stringify(debugUserData) }}</p>
            <p><strong>Статус запроса:</strong> {{ debugStatus }}</p>
            <p><strong>Ответ сервера:</strong> {{ JSON.stringify(debugResponse) }}</p>
            <button @click="toggleDebug" class="debug-btn">Скрыть отладку</button>
        </div>

        <div v-else class="debug-toggle">
            <button @click="toggleDebug" class="debug-btn">Показать отладку</button>
        </div>

        <!-- Основной интерфейс -->
        <div v-if="!debugMode" class="tasks-header">
            <!-- ... остальной код ... -->
        </div>
    </div>
</template>

<script>
export default {
    name: 'TasksView',
    data() {
        return {
            tasks: [],
            newTask: '',
            errorMessage: '',
            // Данные для отладки
            debugMode: true, // по умолчанию показываем отладку
            debugUserData: null,
            debugStatus: '',
            debugResponse: null
        }
    },
    async mounted() {
        await this.fetchTasks()
    },
    methods: {
        toggleDebug() {
            this.debugMode = !this.debugMode
        },
        
        async fetchTasks() {
            try {
                // Сохраняем данные для отладки
                this.debugUserData = window.Telegram?.WebApp?.initDataUnsafe?.user
                this.debugStatus = 'Запрос отправляется...'
                
                console.log('=== DEBUG TELEGRAM DATA ===')
                console.log('Telegram:', !!window.Telegram)
                console.log('WebApp:', !!window.Telegram?.WebApp)
                console.log('User data:', this.debugUserData)
                console.log('InitData:', window.Telegram?.WebApp?.initData)
                console.log('===========================')
                
                const tg_user = window.Telegram.WebApp.initDataUnsafe?.user
                
                if (!tg_user) {
                    this.errorMessage = 'Данные пользователя не получены'
                    this.debugStatus = 'Ошибка: нет данных пользователя'
                    return
                }
                
                const response = await fetch(`https://studious-halibut-6xxg5r5rwg43rj4r.github.dev/api/tasks/${tg_user.id}`)
                this.debugStatus = `Статус ответа: ${response.status}`
                
                const data = await response.json()
                this.debugResponse = data
                console.log('Ответ сервера:', data)
                
                this.tasks = data
                this.debugStatus = 'Успешно!'
                
            } catch (error) {
                console.log('error', error)
                this.errorMessage = 'Ошибка загрузки задач'
                this.debugStatus = `Ошибка: ${error.message}`
            }
        },
        
        // ... остальные методы
    }
}
</script>

<style scoped>
.debug-panel {
    background: #f5f5f5;
    border: 2px solid #007aff;
    border-radius: 8px;
    padding: 16px;
    margin: 16px;
    font-family: monospace;
    font-size: 12px;
}

.debug-panel h3 {
    margin: 0 0 12px 0;
    color: #007aff;
}

.debug-toggle {
    text-align: center;
    margin: 16px;
}

.debug-btn {
    background: #007aff;
    color: white;
    border: none;
    padding: 8px 16px;
    border-radius: 6px;
    cursor: pointer;
    font-size: 12px;
}

.error-message {
    background: #ffebee;
    color: #c62828;
    padding: 16px;
    border-radius: 8px;
    text-align: center;
    margin: 16px;
    border: 1px solid #ffcdd2;
}
</style>
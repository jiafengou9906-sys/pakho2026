<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>周柏豪粉丝见面会2026 - 出勤统计</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <style>
        .pakho-dark { background-color: #121212; }
        .glass-card { 
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(0,0,0,0.05);
        }
        .member-btn-active { background-color: #000 !important; color: #fff !important; border-color: #000 !important; }
    </style>
</head>
<body class="bg-gray-50 min-h-screen font-sans text-gray-900">
    <div id="app" class="max-w-md mx-auto min-h-screen relative pb-10">
        
        <header class="bg-white px-6 py-8 border-b border-gray-100">
            <h1 class="text-2xl font-black tracking-tighter text-black uppercase">Pakho Chau <br><span class="text-gray-400">Fans Meeting 2026</span></h1>
            <div class="mt-4 flex items-center justify-between">
                <span class="text-xs font-bold tracking-widest text-gray-400 uppercase">出勤统计中心</span>
                <button @click="handleAdmin" class="text-xs bg-black text-white px-3 py-1 rounded-full">
                    {{ isAdmin ? '管理模式: 开' : '登入' }}
                </button>
            </div>
        </header>

        <section class="px-6 py-6 overflow-x-auto flex gap-4 no-scrollbar">
            <div v-for="member in members" :key="member" class="flex-shrink-0 w-24 glass-card p-3 rounded-2xl text-center shadow-sm">
                <div class="text-[10px] text-gray-400 mb-1 font-bold">{{ member }}</div>
                <div class="text-lg font-black">{{ getMemberTotal(member) }}</div>
                <div class="text-[10px] text-gray-500">次出勤</div>
            </div>
        </section>

        <main class="px-6 space-y-8">
            <div v-for="(station, sIndex) in stations" :key="sIndex" class="relative group">
                <div class="w-full h-56 rounded-3xl overflow-hidden shadow-lg mb-4 bg-gray-200">
                    <img :src="station.img" class="w-full h-full object-cover grayscale hover:grayscale-0 transition-all duration-500">
                </div>
                
                <div class="flex justify-between items-end mb-3">
                    <div>
                        <h2 class="text-xl font-bold text-black">{{ station.name }}</h2>
                        <p class="text-sm text-gray-400 font-medium">{{ station.date }}</p>
                    </div>
                    <div class="text-right">
                        <span class="text-xs font-black text-gray-300">ATTENDANCE</span>
                        <div class="text-lg font-black text-black leading-none">{{ station.attendees.length }}</div>
                    </div>
                </div>

                <div class="flex flex-wrap gap-2">
                    <button 
                        v-for="m in members" 
                        :key="m"
                        @click="toggleAttendance(sIndex, m)"
                        :disabled="!isAdmin"
                        :class="station.attendees.includes(m) ? 'member-btn-active' : 'bg-white text-gray-400 border-gray-100'"
                        class="px-3 py-1 rounded-lg border text-xs font-bold transition-all">
                        {{ m }}
                    </button>
                </div>
            </div>
        </main>

        <footer class="mt-20 px-6 py-10 text-center border-t border-gray-100">
            <p class="text-[10px] text-gray-300 tracking-[0.2em] uppercase font-bold">Created for J & 大哥 & Friends</p>
        </footer>
    </div>

    <script>
        const { createApp } = Vue
        createApp({
            data() {
                return {
                    isAdmin: false,
                    members: ['J', '大哥', '小9', '星球', '安安', '西瓜', '梨涡', '小蓝', '00'],
                    stations: [
                        { name: '深圳站', date: '1.16 - 1.18', attendees: [], img: 'https://images.unsplash.com/photo-1493225255756-d9584f8606e9?auto=format&fit=crop&w=800&q=80' },
                        { name: '赣州站', date: '1.30 - 2.1', attendees: [], img: 'https://images.unsplash.com/photo-1501386761578-eac5c94b800a?auto=format&fit=crop&w=800&q=80' },
                        { name: '香港站', date: '3.14 - 3.15', attendees: [], img: 'https://images.unsplash.com/photo-1514525253361-bee8718a74a2?auto=format&fit=crop&w=800&q=80' },
                        { name: '汕尾站', date: '5.30 - 6.2', attendees: [], img: 'https://images.unsplash.com/photo-1470225620780-dba8ba36b745?auto=format&fit=crop&w=800&q=80' },
                        { name: '桂林站', date: '日期待定', attendees: [], img: 'https://images.unsplash.com/photo-1533174072545-7a4b6ad7a6c3?auto=format&fit=crop&w=800&q=80' },
                        { name: '武汉/其他', date: '日期待定', attendees: [], img: 'https://images.unsplash.com/photo-1459749411177-042180ce673c?auto=format&fit=crop&w=800&q=80' }
                    ]
                }
            },
            mounted() {
                const saved = localStorage.getItem('pakho_2026_v2');
                if (saved) this.stations = JSON.parse(saved);
            },
            methods: {
                handleAdmin() {
                    if (this.isAdmin) {
                        this.isAdmin = false;
                    } else {
                        const pw = prompt('请输入编辑密码:');
                        if (pw === '20251226') {
                            this.isAdmin = true;
                            alert('管理模式已开启');
                        } else {
                            alert('密码错误');
                        }
                    }
                },
                toggleAttendance(sIndex, member) {
                    if (!this.isAdmin) return;
                    const list = this.stations[sIndex].attendees;
                    const index = list.indexOf(member);
                    if (index > -1) list.splice(index, 1);
                    else list.push(member);
                    this.save();
                },
                getMemberTotal(member) {
                    return this.stations.filter(s => s.attendees.includes(member)).length;
                },
                save() {
                    localStorage.setItem('pakho_2026_v2', JSON.stringify(this.stations));
                }
            }
        }).mount('#app')
    </script>
</body>
</html>

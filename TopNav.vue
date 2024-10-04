<template>
    <!-- Contenedor principal de la barra de navegación superior -->
    <div 
        id="TopNav" 
        class="fixed bg-white z-30 flex items-center w-full border-b h-[61px]"
    >
        <!-- Contenedor interno que ajusta el ancho basado en la ruta actual -->
        <div 
            :class="route.fullPath === '/' ? 'max-w-[1150px]' : ''" 
            class="flex items-center justify-between w-full px-6 mx-auto"
        >
            <!-- Logo, con un tamaño de ancho que cambia según la ruta actual -->
            <div :class="route.fullPath === '/' ? 'w-[80%]' : 'lg:w-[20%] w-[70%]'">
                <NuxtLink to="/">
                    <!-- Imagen del logo -->
                    <img width="115" src="/assets/img/tiktok-logo.png">
                </NuxtLink>
            </div>

            <!-- Barra de búsqueda, oculta en dispositivos pequeños (md:flex) -->
            <div class="hidden md:flex items-center bg-[#F1F1F2] p-1 rounded-full max-w-[380px] w-full ">
                <!-- Campo de entrada para búsqueda de cuentas -->
                <input 
                    type="text" 
                    class="w-full pl-3 my-2 bg-transparent placeholder-[#838383] text-[15px] focus:outline-none"
                    placeholder="Search accounts"
                >
                <!-- Icono de búsqueda junto a la barra de búsqueda -->
                <div class="px-3 py-1 flex items-center border-l border-l-gray-300">
                    <Icon name="ri:search-line" color="#A1A2A7" size="22"/>
                </div>
            </div>

            <!-- Contenedor de los botones en la parte derecha del nav -->
            <div class="flex items-center justify-end gap-3 min-w-[275px] max-w-[320px] w-full">
                <!-- Botón de subir contenido, que verifica si el usuario está logueado -->
                <button 
                    @click="isLoggedIn()" 
                    class="flex items-center border rounded-sm px-3 py-[6px] hover:bg-gray-100"
                >
                    <Icon name="mdi:plus" color="#000000" size="22"/>
                    <span class="px-2 font-medium text-[15px]">Upload</span>
                </button>

                <!-- Mostrar botón de login si el usuario no está logueado ($userStore.id no existe) -->
                <div v-if="$userStore && !$userStore.id" class="flex items-center">
                    <button 
                        @click="$generalStore.isLoginOpen = true" 
                        class="flex items-center bg-[#F02C56] text-white border rounded-md px-3 py-[6px]"
                    >
                        <span class="mx-4 font-medium text-[15px]">Log in</span>
                    </button>
                    <!-- Icono de menú vertical -->
                    <Icon name="mdi:dots-vertical" color="#161724" size="25"/>
                </div>

                <!-- Si el usuario está logueado ($userStore.id existe), mostrar opciones -->
                <div v-else class="flex items-center">
                    <!-- Icono de enviar -->
                    <Icon class="ml-1 mr-4" name="carbon:send-alt" color="#161724" size="30"/>
                    <!-- Icono de mensajes -->
                    <Icon class="mr-5" name="bx:message-detail" color="#161724" size="27"/>
                    <!-- Imagen de perfil del usuario y botón para mostrar menú -->
                    <div class="relative">
                        <button 
                            class="mt-1"
                            @click="showMenu = !showMenu" <!-- Alterna la visibilidad del menú -->
                        >
                            <!-- Imagen de perfil del usuario -->
                            <img 
                                class="rounded-full" 
                                width="33" 
                                :src="$userStore?.image" <!-- Verificar que $userStore.image existe -->
                            >
                        </button>

                        <!-- Menú desplegable de opciones del usuario -->
                        <div 
                            v-if="showMenu" 
                            id="PopupMenu"
                            class="absolute bg-white rounded-lg py-1.5 w-[200px] shadow-xl border top-[43px] -right-2"
                        >
                            <!-- Enlace al perfil del usuario -->
                            <NuxtLink 
                                :to="`/profile/${$userStore?.id}`" <!-- Verificar que el id existe -->
                                @click="showMenu = false" <!-- Cerrar el menú al hacer clic -->
                                class="flex items-center justify-start py-3 px-2 hover:bg-gray-100 cursor-pointer"
                            >
                                <Icon name="ph:user" size="20"/>
                                <span class="pl-2 font-semibold text-sm">Profile</span>
                            </NuxtLink>
                            <!-- Opción para cerrar sesión -->
                            <div 
                                @click="logout()"
                                class="flex items-center justify-start py-3 px-1.5 hover:bg-gray-100 border-t cursor-pointer"
                            >
                                <Icon name="ic:outline-login" size="20"/>
                                <span class="pl-2 font-semibold text-sm">Log out</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
// Importamos $userStore y $generalStore desde NuxtApp
const { $userStore = {}, $generalStore } = useNuxtApp()

// Obtenemos las rutas y el router de Vue
const route = useRoute()
const router = useRouter()

// Variable reactiva para mostrar u ocultar el menú
let showMenu = ref(false)

// Cuando el componente está montado, añadimos un evento para cerrar el menú al hacer clic fuera de él
onMounted(() => {
    document.addEventListener('mouseup', function(e) {
        let popupMenu = document.getElementById('PopupMenu');
        if (popupMenu && !popupMenu.contains(e.target)) { // Verificamos que popupMenu existe
            showMenu.value = false // Cerramos el menú si se hace clic fuera de él
        }
    });
})

// Función para verificar si el usuario está logueado
const isLoggedIn = () => {
    if ($userStore?.id) { // Verificar si el id existe en $userStore
        router.push('/upload') // Si está logueado, redirigimos a la página de subir contenido
    } else {
        $generalStore.isLoginOpen = true // Si no está logueado, abrimos el modal de login
    }
}

// Función para cerrar sesión
const logout = () => {
    try {
        $userStore.logout() // Llamamos al método de logout del userStore
        router.push('/') // Redirigimos a la página de inicio
    } catch (error) {
        console.log(error) // Si hay un error, lo mostramos en consola
    }
}
</script>

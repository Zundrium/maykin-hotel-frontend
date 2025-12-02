<script lang="ts">
    import { onMount } from "svelte";
    import Select from "svelte-select";
    import type { City, Hotel } from "$lib/types";

    let cities = $state<City[]>([]);
    let hotels = $state<Hotel[]>([]);

    let selectedCity = $state<City | null>(null);
    let selectedHotel = $state<Hotel | null>(null);

    let loadingCities = $state(true);
    let loadingHotels = $state(false);

    onMount(async () => {
        try {
            const res = await fetch("http://localhost:8000/cities?format=json");
            if (res.ok) {
                cities = await res.json();
            } else {
                console.error("Failed to fetch cities");
            }
        } catch (e) {
            console.error("Error fetching cities:", e);
        } finally {
            loadingCities = false;
        }
    });

    function handleCityChange(event: any) {
        const city = event.detail;
        console.log("City changed:", city);
        selectedCity = city;

        if (city) {
            loadHotels(city.id);
        } else {
            hotels = [];
            selectedHotel = null;
        }
    }

    function handleCityClear() {
        console.log("City cleared");
        selectedCity = null;
        hotels = [];
        selectedHotel = null;
    }

    async function loadHotels(cityId: number) {
        console.log("Loading hotels for city ID:", cityId);
        loadingHotels = true;
        selectedHotel = null; // Reset hotel selection
        try {
            const res = await fetch(
                `http://localhost:8000/hotels?format=json&city=${cityId}`,
            );
            if (res.ok) {
                hotels = await res.json();
                for (const hotel of hotels) {
                    hotel.label = `${hotel.name} (${hotel.zone})`;
                }
                console.log("Loaded hotels:", hotels);
            } else {
                console.error("Failed to fetch hotels");
                hotels = [];
            }
        } catch (e) {
            console.error("Error fetching hotels:", e);
            hotels = [];
        } finally {
            loadingHotels = false;
        }
    }
</script>

<div
    class="min-h-screen bg-gradient-to-br from-indigo-500 via-purple-500 to-pink-500 flex items-center justify-center p-4"
>
    <div
        class="bg-white/95 backdrop-blur-md rounded-2xl shadow-2xl p-8 max-w-md w-full space-y-8"
    >
        <div class="text-center">
            <h1 class="text-3xl font-bold text-gray-900 tracking-tight">
                Find Your Stay
            </h1>
            <p class="mt-2 text-sm text-gray-600">
                Select a city to discover exclusive hotels
            </p>
        </div>

        <div class="space-y-6">
            <!-- City Selection -->
            <div class="relative">
                <label
                    for="city"
                    class="block text-sm font-medium text-gray-700 mb-1"
                    >Destination</label
                >
                <Select
                    id="city"
                    items={cities}
                    label="name"
                    bind:value={selectedCity}
                    itemId="id"
                    placeholder="Select a city..."
                    disabled={loadingCities}
                    on:change={handleCityChange}
                    on:clear={handleCityClear}
                    class="svelte-select-custom"
                />
            </div>

            <!-- Hotel Selection -->
            <div class="relative">
                <label
                    for="hotel"
                    class="block text-sm font-medium text-gray-700 mb-1"
                    >Hotel</label
                >
                <Select
                    id="hotel"
                    items={hotels}
                    bind:value={selectedHotel}
                    itemId="id"
                    placeholder={!selectedCity
                        ? "First select a city"
                        : loadingHotels
                          ? "Loading hotels..."
                          : "Select a hotel..."}
                    disabled={!selectedCity || loadingHotels}
                    class="svelte-select-custom"
                />
            </div>
        </div>

        {#if selectedHotel}
            <div
                class="mt-8 p-4 bg-indigo-50 rounded-lg border border-indigo-100 animate-fade-in"
            >
                <p class="text-indigo-800 text-center font-medium">
                    Great choice! You've selected <span class="font-bold"
                        >{selectedHotel.label}</span
                    >.
                </p>
            </div>
        {/if}
    </div>
</div>

<style>
    @keyframes fade-in {
        from {
            opacity: 0;
            transform: translateY(10px);
        }
        to {
            opacity: 1;
            transform: translateY(0);
        }
    }
    .animate-fade-in {
        animation: fade-in 0.3s ease-out forwards;
    }

    /* Custom styling for svelte-select to match Tailwind theme */
    :global(.svelte-select-custom) {
        --background: #f9fafb;
        --border: 1px solid #d1d5db;
        --border-radius: 0.5rem;
        --height: 46px; /* Match standard input height */
        --placeholder-color: #6b7280;
        --item-color: #374151;
        --item-hover-bg: #e0e7ff; /* Indigo-100 */
        --item-is-active-bg: #6366f1; /* Indigo-500 */
        --item-is-active-color: white;
    }
    :global(.svelte-select-custom .svelte-select-list) {
        box-shadow:
            0 4px 6px -1px rgba(0, 0, 0, 0.1),
            0 2px 4px -1px rgba(0, 0, 0, 0.06);
    }
</style>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ulam Roulette 🎲</title>
    <!-- PWA Tags -->
    <link rel="manifest" href="manifest.json">
    <meta name="theme-color" content="#ea580c">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <link rel="apple-touch-icon" href="https://cdn-icons-png.flaticon.com/512/1046/1046747.png">
    
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Confetti Library -->
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&display=swap');
        body { font-family: 'Inter', sans-serif; background-color: #fdfbf7; }
        .slot-shuffle { animation: shuffle 0.1s infinite linear; }
        @keyframes shuffle {
            0% { transform: translateY(-2px); opacity: 0.8; }
            50% { transform: translateY(2px); opacity: 1; }
            100% { transform: translateY(-2px); opacity: 0.8; }
        }
        .pop-effect { animation: pop 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
        @keyframes pop {
            0% { transform: scale(0.8); }
            100% { transform: scale(1); }
        }
    </style>
</head>
<body class="pb-20">

    <header class="bg-orange-600 text-white p-6 text-center shadow-lg">
        <h1 class="text-3xl font-extrabold tracking-tighter">ULAM LOTTO 🎰</h1>
        <p class="text-sm font-medium opacity-90 uppercase tracking-widest">Jackpot sa Gutom (180 Recipes!)</p>
    </header>

    <main class="max-w-md mx-auto p-4 space-y-6">
        
        <!-- Reset Button -->
        <div class="text-right">
            <button onclick="resetRecipes()" class="text-[10px] text-gray-400 hover:text-red-500 transition uppercase font-bold tracking-widest">Reset to 180 Recipes 🔄</button>
        </div>

        <section class="bg-white p-8 rounded-[2.5rem] shadow-xl border-4 border-orange-100 text-center relative overflow-hidden">
            <h2 class="text-gray-400 text-xs font-bold uppercase tracking-widest mb-6">Piliin ang Bola</h2>
            
            <div class="grid grid-cols-3 gap-4 mb-8">
                <button onclick="spin('Breakfast')" class="group bg-yellow-400 hover:bg-yellow-500 text-white p-4 rounded-2xl shadow-md transition active:scale-90">
                    <span class="block text-2xl">🍳</span>
                    <span class="text-[10px] font-black uppercase">Agahan</span>
                </button>
                <button onclick="spin('Lunch')" class="group bg-orange-500 hover:bg-orange-600 text-white p-4 rounded-2xl shadow-md transition active:scale-90">
                    <span class="block text-2xl">🍚</span>
                    <span class="text-[10px] font-black uppercase">Tanghalian</span>
                </button>
                <button onclick="spin('Dinner')" class="group bg-red-500 hover:bg-red-600 text-white p-4 rounded-2xl shadow-md transition active:scale-90">
                    <span class="block text-2xl">🍲</span>
                    <span class="text-[10px] font-black uppercase">Hapunan</span>
                </button>
            </div>

            <div id="display-screen" class="bg-gray-900 rounded-3xl p-8 min-h-[160px] flex flex-col items-center justify-center border-8 border-gray-800 shadow-inner">
                <div id="slot-container" class="text-center">
                    <p id="instruction-text" class="text-orange-400 text-sm font-mono animate-pulse text-center uppercase">Choose a category</p>
                    <h3 id="meal-name" class="text-2xl font-black text-white leading-tight hidden text-center"></h3>
                </div>
            </div>

            <div id="recipe-details" class="hidden mt-6 text-left p-5 bg-orange-50 rounded-2xl border border-orange-200 pop-effect">
                <div class="space-y-3 text-sm text-gray-800">
                    <p><strong>🛒 Sangkap:</strong> <span id="meal-ingredients" class="text-gray-600 text-xs italic"></span></p>
                    <p><strong>👨‍🍳 Steps:</strong> <div id="meal-steps" class="text-gray-600 text-xs whitespace-pre-line leading-relaxed"></div></p>
                </div>
            </div>
        </section>

        <section class="bg-white p-6 rounded-3xl shadow-md border border-gray-100">
            <h2 class="text-lg font-bold mb-4 text-gray-800 flex items-center gap-2">
                <span>➕</span> Dagdag Menu
            </h2>
            <form id="recipe-form" class="space-y-3">
                <input type="text" id="form-name" placeholder="Pangalan ng Ulam" class="w-full p-4 bg-gray-50 rounded-2xl border-none ring-1 ring-gray-200 focus:ring-2 focus:ring-orange-400 outline-none" required>
                <div class="grid grid-cols-2 gap-2">
                    <select id="form-category" class="p-4 bg-gray-50 rounded-2xl border-none ring-1 ring-gray-200 focus:ring-2 focus:ring-orange-400 outline-none">
                        <option value="Breakfast">Breakfast</option>
                        <option value="Lunch">Lunch</option>
                        <option value="Dinner">Dinner</option>
                    </select>
                    <button type="submit" class="bg-gray-900 text-white font-black py-4 rounded-2xl hover:bg-black transition">SAVE</button>
                </div>
                <textarea id="form-ingredients" placeholder="Mga Sangkap..." class="w-full p-4 bg-gray-50 rounded-2xl border-none ring-1 ring-gray-200 focus:ring-2 focus:ring-orange-400 outline-none" rows="2"></textarea>
                <textarea id="form-steps" placeholder="Paano lutuin?..." class="w-full p-4 bg-gray-50 rounded-2xl border-none ring-1 ring-gray-200 focus:ring-2 focus:ring-orange-400 outline-none" rows="2"></textarea>
            </form>
        </section>
    </main>

    <script>
        const starterRecipes = [
            // Traditional (1-10)
            { name: "Tortang Talong", category: "Breakfast", ingredients: "Talong, Itlog, Asin", steps: "1. Grill eggplant until charred.\n2. Peel off skin while keeping the stem.\n3. Flatten with a fork.\n4. Dip in beaten eggs with salt.\n5. Pan-fry until golden brown." },
            { name: "Gisadong Corned Beef", category: "Breakfast", ingredients: "Corned Beef, Patatas, Sibuyas", steps: "1. Sauté garlic and onions until fragrant.\n2. Fry potato cubes until soft and slightly golden.\n3. Add corned beef and sauté for 3-5 mins.\n4. Optional: Add a little water for more sauce." },
            { name: "Sardines with Misua", category: "Lunch", ingredients: "Sardinas, Misua, Patola", steps: "1. Sauté garlic and onions.\n2. Add canned sardines and sauté for 1 min.\n3. Pour 2 cups water and let boil.\n4. Add patola slices until tender.\n5. Drop misua noodles and cook for 30 seconds. Season with patis." },
            { name: "Adobong Sitaw", category: "Lunch", ingredients: "Sitaw, Toyo, Suka, Bawang", steps: "1. Sauté garlic until brown.\n2. Add sitaw (string beans) and sauté briefly.\n3. Pour soy sauce, vinegar, and a bit of water.\n4. Simmer until sitaw is tender and sauce reduces. Do not stir immediately after adding vinegar." },
            { name: "Fried Galunggong", category: "Lunch", ingredients: "Galunggong, Asin, Mantika", steps: "1. Clean the fish and pat dry.\n2. Rub with salt inside and out.\n3. Fry in hot oil until very crispy.\n4. Serve with kamatis and bagoong." },
            { name: "Ginisang Monggo", category: "Lunch", ingredients: "Monggo, Malunggay, Tinapa", steps: "1. Boil munggo until soft.\n2. In another pan, sauté garlic, onion, and tinapa flakes.\n3. Add the boiled monggo and malunggay leaves.\n4. Simmer for 5 mins and top with crushed chicharon." },
            { name: "Century Tuna Sisig", category: "Dinner", ingredients: "Tuna, Sibuyas, Mayo, Sili", steps: "1. Drain tuna oil.\n2. Sauté plenty of chopped onions and chili.\n3. Add tuna and cook until slightly toasted.\n4. Turn off heat and mix in mayonnaise and a squeeze of calamansi." },
            { name: "Egg Drop Sopas", category: "Breakfast", ingredients: "Macaroni, Gatas, Itlog", steps: "1. Boil macaroni in water with salt until tender.\n2. Add evaporated milk and stir.\n3. While boiling, slowly pour beaten egg while stirring to create ribbons.\n4. Season with salt and pepper." },
            { name: "Ginisang Sayote", category: "Dinner", ingredients: "Sayote, Garlic, Onion", steps: "1. Sauté garlic and onions.\n2. Add sliced sayote.\n3. Pour a little water and season with patis.\n4. Cook until sayote is crisp-tender. Do not overcook." },
            { name: "Scrambled Egg w/ Kamatis", category: "Breakfast", ingredients: "Itlog, Kamatis, Sibuyas", steps: "1. Sauté onions and tomatoes until very soft and mushy.\n2. Add beaten eggs.\n3. Scramble gently until cooked but still moist." },
            
            // Student Staples (11-20)
            { name: "Ginisang Ampalaya", category: "Lunch", ingredients: "Ampalaya, Itlog, Kamatis", steps: "1. Slice ampalaya thinly and soak in salt water to reduce bitterness.\n2. Sauté garlic, onions, and tomatoes.\n3. Add ampalaya and sauté for 3 mins.\n4. Pour beaten eggs and scramble until set." },
            { name: "Adobong Itlog", category: "Breakfast", ingredients: "Egg, Toyo, Suka", steps: "1. Boil eggs for 10 mins and peel.\n2. Sauté garlic.\n3. Add soy sauce, vinegar, peppercorns, and water.\n4. Simmer the peeled eggs in the sauce for 5 mins until they turn brown." },
            { name: "Pancit Canton with Egg", category: "Dinner", ingredients: "Instant Canton, Itlog", steps: "1. Cook instant noodles as per package.\n2. Boil an egg or fry it sunny-side up.\n3. Mix noodles with seasonings and top with the egg and some sautéed veggies if available." },
            { name: "Ginisang Repolyo", category: "Lunch", ingredients: "Repolyo, Corned Beef", steps: "1. Sauté onions and corned beef.\n2. Add shredded repolyo (cabbage).\n3. Sauté for 2-3 mins until cabbage is soft but still has a bite." },
            { name: "Fried Tokwa", category: "Dinner", ingredients: "Tokwa, Toyo, Suka", steps: "1. Cube the tofu and pat dry.\n2. Fry in hot oil until all sides are golden and crispy.\n3. Serve with a dip made of soy sauce, vinegar, onion, and chili." },
            { name: "Odong with Sardines", category: "Lunch", ingredients: "Odong Noodles, Sardinas", steps: "1. Sauté garlic and canned sardines.\n2. Add 2 cups water and let boil.\n3. Add odong noodles and cook until tender.\n4. Season with salt or patis." },
            { name: "Skinless Longganisa", category: "Breakfast", ingredients: "Longganisa, Sinangag", steps: "1. Fry longganisa in a little water first until water evaporates.\n2. Add oil and fry until caramelized.\n3. Serve with garlic fried rice (sinangag)." },
            { name: "Ginisang Kalabasa", category: "Lunch", ingredients: "Kalabasa, Sitaw, Bagoong", steps: "1. Sauté garlic and onions.\n2. Add squash (kalabasa) cubes and a little water. Cook until slightly soft.\n3. Add sitaw (string beans) and a tablespoon of bagoong.\n4. Simmer until all veggies are tender." },
            { name: "Fried Spam / Maling", category: "Breakfast", ingredients: "Spam, Itlog", steps: "1. Slice luncheon meat into squares.\n2. Fry until crispy on the edges.\n3. Serve with scrambled or sunny-side up eggs." },
            { name: "Gisadong Beef Loaf", category: "Dinner", ingredients: "Beef Loaf, Sibuyas, Itlog", steps: "1. Mash beef loaf with a fork.\n2. Sauté with plenty of onions.\n3. Mix in an egg and scramble until cooked." },

            // Market Quickies (21-40)
            { name: "Fried Hotdog", category: "Breakfast", ingredients: "Hotdog, Mantika", steps: "1. Make small diagonal cuts on the hotdog.\n2. Fry in hot oil until they 'bloom' or puff up.\n3. Serve with rice and catsup." },
            { name: "Salted Egg w/ Kamatis", category: "Breakfast", ingredients: "Itlog na Pula, Kamatis", steps: "1. Cut salted eggs in half or cubes.\n2. Chop fresh tomatoes and onions.\n3. Mix together in a bowl. Best paired with fried fish." },
            { name: "Fried Tuyo", category: "Breakfast", ingredients: "Tuyo, Suka", steps: "1. Fry dried fish (tuyo) quickly in hot oil (careful, it smells strong!).\n2. Serve with spicy vinegar dip and hot rice." },
            { name: "Daing na Bangus", category: "Lunch", ingredients: "Bangus, Suka, Bawang", steps: "1. Marinate bangus in vinegar, crushed garlic, and pepper overnight.\n2. Fry until the skin is golden and the garlic bits are toasted." },
            { name: "Fried Tilapia", category: "Lunch", ingredients: "Tilapia, Asin", steps: "1. Clean tilapia and score the sides.\n2. Rub with salt.\n3. Deep fry until the fins and skin are crispy." },
            { name: "Fried Chicken (Easy)", category: "Lunch", ingredients: "Manok, Breading Mix", steps: "1. Wash chicken and pat dry.\n2. Coat evenly with store-bought breading mix.\n3. Fry in medium heat until fully cooked and golden." },
            { name: "Pork Chop (Pan-fry)", category: "Dinner", ingredients: "Pork Chop, Calamansi", steps: "1. Marinate pork in calamansi and salt for 15 mins.\n2. Fry in a pan with medium heat until browned and cooked through." },
            { name: "Lucky Me w/ Egg", category: "Dinner", ingredients: "Instant Noodles, Itlog", steps: "1. Boil water and cook instant noodles.\n2. While boiling, drop a raw egg and let it poach for 2 mins without stirring much.\n3. Pour into a bowl with seasoning." },
            { name: "Fried Lumpia (Market)", category: "Lunch", ingredients: "Ready-to-fry Lumpia", steps: "1. Heat oil in a pan.\n2. Fry frozen or fresh lumpia until the wrapper is crispy and brown.\n3. Drain excess oil." },
            { name: "Ginisang Pechay", category: "Lunch", ingredients: "Pechay, Patis", steps: "1. Sauté garlic.\n2. Add pechay stems first, sauté for 1 min.\n3. Add leaves and season with patis.\n4. Cook for 30 seconds more. Keep it crunchy!" },
            { name: "Sinigang Mix Isda", category: "Lunch", ingredients: "Isda, Sinigang Mix, Kangkong", steps: "1. Boil water with sinigang mix and tomatoes.\n2. Add sliced fish (tilapia or bangus).\n3. Add kangkong leaves last and turn off heat after 1 min." },
            { name: "Nilagang Baboy (Simple)", category: "Dinner", ingredients: "Baboy, Patatas, Repolyo", steps: "1. Boil pork cubes until tender (about 45 mins).\n2. Add potato cubes and cook until soft.\n3. Add cabbage leaves and season with salt and peppercorns." },
            { name: "Corned Beef w/ Cabbage", category: "Lunch", ingredients: "Corned Beef, Repolyo", steps: "1. Sauté onions.\n2. Add corned beef.\n3. Add lots of shredded cabbage and sauté until cabbage is wilted but sweet." },
            { name: "Fried Liempo (Quick)", category: "Dinner", ingredients: "Liempo, Asin", steps: "1. Rub pork belly with salt and pepper.\n2. Pan-fry in its own fat until the skin is crispy and meat is brown." },
            { name: "Tinolang Manok (Simple)", category: "Dinner", ingredients: "Manok, Sayote, Luya", steps: "1. Sauté ginger, garlic, and onions.\n2. Add chicken and sear until brown.\n3. Add water and sayote. Simmer until chicken is tender." },
            { name: "Fried Talong w/ Bagoong", category: "Lunch", ingredients: "Talong, Bagoong", steps: "1. Slice eggplant diagonally.\n2. Fry until soft and brown.\n3. Serve with a side of sautéed bagoong (shrimp paste)." },
            { name: "Century Tuna w/ Skyflakes", category: "Dinner", ingredients: "Tuna, Skyflakes", steps: "1. Mix canned tuna with chopped onions and mayo.\n2. Crush Skyflakes and mix it in for texture.\n3. Eat with rice or as a snack." },
            { name: "Pork Adobo (Basic)", category: "Dinner", ingredients: "Baboy, Toyo, Suka", steps: "1. Simmer pork in soy sauce, vinegar, garlic, and bay leaves.\n2. Cook until meat is tender and sauce is thick/oily." },
            { name: "Ginataang Gulay (Mix)", category: "Lunch", ingredients: "Gulay Mix, Gata", steps: "1. Sauté garlic.\n2. Add market veggie mix (sitaw, kalabasa).\n3. Pour coconut milk (gata) and simmer until veggies are soft and sauce is thick." },
            { name: "Scrambled Egg (Basic)", category: "Breakfast", ingredients: "Itlog, Asin", steps: "1. Beat 2 eggs with a pinch of salt.\n2. Fry in a hot pan with a little oil while stirring continuously for fluffiness." },

            // Breakfast Expansion (41-60)
            { name: "Instant Champorado", category: "Breakfast", ingredients: "Champorado Mix, Gatas", steps: "1. Boil water and add champorado mix.\n2. Stir constantly until rice is cooked and mixture thickens.\n3. Serve with evaporated milk and sugar to taste." },
            { name: "Instant Arroz Caldo", category: "Breakfast", ingredients: "Arroz Caldo Mix, Egg", steps: "1. Boil water and add arroz caldo mix.\n2. Stir until thickened.\n3. Add a hard-boiled egg and top with toasted garlic." },
            { name: "Pandesal w/ Peanut Butter", category: "Breakfast", ingredients: "Pandesal, Peanut Butter", steps: "1. Slice pandesal in half.\n2. Toast in a pan or oven.\n3. Spread peanut butter generously." },
            { name: "Sardines w/ Fried Egg", category: "Breakfast", ingredients: "Sardines, Egg", steps: "1. Heat sardines in a pan with some onions.\n2. Fry an egg sunny-side up separately.\n3. Mix the sardine sauce with rice and top with the egg." },
            { name: "Fried Siomai", category: "Breakfast", ingredients: "Frozen Siomai, Toyo", steps: "1. Thaw siomai slightly.\n2. Deep fry until the wrapper is golden and crispy.\n3. Serve with soy sauce and calamansi." },
            { name: "Pork Tocino", category: "Breakfast", ingredients: "Tocino (Market buy)", steps: "1. Put tocino in a pan with 1/2 cup water.\n2. Boil until water is gone.\n3. Add oil and fry until the sugar caramelizes and turns reddish-brown." },
            { name: "Beef Tapa", category: "Breakfast", ingredients: "Tapa (Market buy)", steps: "1. Fry beef tapa in a little oil until cooked through and slightly crispy on the edges.\n2. Serve with vinegar dip." },
            { name: "Cabanatuan Longganisa", category: "Breakfast", ingredients: "Longganisa, Suka", steps: "1. Fry longganisa until dark brown and aromatic.\n2. Serve with lots of garlic rice and vinegar." },
            { name: "Pandesal w/ Liverspread", category: "Breakfast", ingredients: "Pandesal, Liverspread", steps: "1. Spread Reno or liverspread on warm pandesal.\n2. Best paired with hot coffee." },
            { name: "Tuna Omelette", category: "Breakfast", ingredients: "Canned Tuna, Egg, Onion", steps: "1. Mix drained tuna, 2 eggs, and chopped onions.\n2. Pour into a flat pan and cook like a pancake.\n3. Flip and cook the other side." },
            { name: "Fried Rice (Sinangag)", category: "Breakfast", ingredients: "Bahaw, Bawang", steps: "1. Sauté lots of crushed garlic until brown.\n2. Add leftover rice (bahaw).\n3. Season with salt and toss until rice is hot and coated in garlic oil." },
            { name: "Pancake (Instant Mix)", category: "Breakfast", ingredients: "Pancake Mix, Mantikilya", steps: "1. Mix pancake flour with water/milk and egg.\n2. Pour onto a buttered pan.\n3. Flip when bubbles appear on top." },
            { name: "Lugaw w/ Tokwa", category: "Breakfast", ingredients: "Bigas, Tokwa, Luya", steps: "1. Boil rice in lots of water with ginger until it breaks down into porridge.\n2. Fry tofu cubes separately until crispy.\n3. Top lugaw with tofu and spring onions." },
            { name: "Fried Saba", category: "Breakfast", ingredients: "Saging na Saba", steps: "1. Slice ripe saba bananas lengthwise.\n2. Fry in oil until golden brown. Optional: Sprinkle with brown sugar while frying." },
            { name: "Steamed Egg", category: "Breakfast", ingredients: "Egg, Toyo, Sesame Oil", steps: "1. Beat an egg with equal parts water.\n2. Steam over low heat for 10 mins until it looks like custard.\n3. Drizzle with soy sauce and sesame oil." },
            { name: "Oatmeal w/ Banana", category: "Breakfast", ingredients: "Oats, Saging, Gatas", steps: "1. Cook oats in boiling water/milk.\n2. Add sliced bananas and a pinch of salt.\n3. Sweeten with sugar or honey." },
            { name: "Hard Boiled Egg w/ Bagoong", category: "Breakfast", ingredients: "Egg, Bagoong Alamang", steps: "1. Boil eggs for 9 mins.\n2. Peel and serve with a side of sautéed bagoong." },
            { name: "Spanish Sardines w/ Pandesal", category: "Breakfast", ingredients: "Spanish Sardines, Pandesal", steps: "1. Open a bottle of Spanish sardines.\n2. Stuff the fish and some oil into warm pandesal." },
            { name: "Sunny Side Up (Crispy)", category: "Breakfast", ingredients: "Egg, Asin", steps: "1. Heat oil until very hot.\n2. Crack egg and let the edges crisp up while the yolk stays runny.\n3. Season with salt." },
            { name: "Ham and Egg", category: "Breakfast", ingredients: "Sweet Ham, Egg", steps: "1. Fry ham slices until edges are brown.\n2. Scramble an egg in the same pan to soak up the ham flavor." },

            // Lunch Expansion (61-80)
            { name: "Ginisang Toge", category: "Lunch", ingredients: "Toge, Tokwa, Carrot", steps: "1. Sauté garlic and onions.\n2. Add fried tofu cubes and carrots.\n3. Add bean sprouts (toge) and sauté for only 2 mins to keep them crunchy." },
            { name: "Adobong Kangkong", category: "Lunch", ingredients: "Kangkong, Toyo, Suka", steps: "1. Sauté lots of garlic.\n2. Add kangkong stems first, then the leaves.\n3. Add soy sauce and vinegar. Simmer briefly." },
            { name: "Ginisang Upo", category: "Lunch", ingredients: "Upo, Pork Bits/Shrimp", steps: "1. Sauté pork bits until brown.\n2. Add garlic, onion, and sliced upo.\n3. Season with salt and cook until upo is transparent." },
            { name: "Pork Giniling (Simple)", category: "Lunch", ingredients: "Pork Giniling, Patatas", steps: "1. Sauté ground pork until brown.\n2. Add diced potatoes and carrots.\n3. Pour a little soy sauce and water. Simmer until veggies are soft." },
            { name: "Chicken Afritada (Easy)", category: "Lunch", ingredients: "Chicken, Tomato Sauce", steps: "1. Sauté chicken pieces.\n2. Add tomato sauce, water, and potatoes.\n3. Simmer until sauce is thick and chicken is tender." },
            { name: "Pork Menudo (Simple)", category: "Lunch", ingredients: "Pork, Atay, Menudo Mix", steps: "1. Sauté pork and liver.\n2. Add menudo mix and water.\n3. Add potatoes and carrots. Simmer until thick." },
            { name: "Chop Suey (Market Mix)", category: "Lunch", ingredients: "Chop Suey Veggie Mix", steps: "1. Sauté garlic and onions.\n2. Add mixed veggies from the market.\n3. Add oyster sauce and a cornstarch slurry to thicken the sauce." },
            { name: "Fish Fillet (Frozen)", category: "Lunch", ingredients: "Fish Fillet, Mayo", steps: "1. Thaw and pat dry fish fillet.\n2. Season with salt/pepper and fry until golden.\n3. Serve with garlic-mayo dip." },
            { name: "Salted Egg w/ Pipino", category: "Lunch", ingredients: "Itlog na Pula, Pipino", steps: "1. Slice pipino (cucumber) thinly.\n2. Mix with chopped salted eggs and a splash of vinegar." },
            { name: "Ginisang Baguio Beans", category: "Lunch", ingredients: "Baguio Beans, Pork Bits", steps: "1. Sauté garlic and pork.\n2. Add Baguio beans and sauté for 3 mins.\n3. Season with soy sauce." },
            { name: "Adobong Atay/Balunbalunan", category: "Lunch", ingredients: "Atay, Balunbalunan, Bawang", steps: "1. Clean and boil gizzard (balunbalunan) until tender.\n2. Sauté with liver in soy sauce, vinegar, and garlic." },
            { name: "Boneless Fried Bangus", category: "Lunch", ingredients: "Bangus, Garlic", steps: "1. Fry boneless milkfish in hot oil skin-side down first.\n2. Cook until the skin is very crispy." },
            { name: "Crispy Chicken Skin", category: "Lunch", ingredients: "Chicken Skin, Flour", steps: "1. Clean chicken skin and remove excess fat.\n2. Coat in seasoned flour.\n3. Deep fry until golden and airy-crisp." },
            { name: "Ginisang Patola w/ Misua", category: "Lunch", ingredients: "Patola, Misua, Bawang", steps: "1. Sauté garlic.\n2. Add patola slices and sauté.\n3. Add water and let boil. Drop misua last and turn off heat." },
            { name: "Bicol Express (Easy)", category: "Lunch", ingredients: "Pork, Gata, Sili", steps: "1. Sauté pork until brown.\n2. Add lots of chopped siling haba.\n3. Pour coconut milk and simmer until oily." },
            { name: "Ginisang Kalabasa/Malunggay", category: "Lunch", ingredients: "Kalabasa, Malunggay", steps: "1. Boil squash until soft.\n2. Mash some of it to thicken the soup.\n3. Add malunggay leaves and season with salt." },
            { name: "Fried Pork Belly", category: "Lunch", ingredients: "Pork Belly, Asin", steps: "1. Season liempo with salt.\n2. Fry in a pan until the fat renders and the skin becomes crispy." },
            { name: "Chicken Curry (Instant)", category: "Lunch", ingredients: "Chicken, Curry Powder, Gata", steps: "1. Sauté chicken and potatoes.\n2. Add curry powder and coconut milk.\n3. Simmer until chicken is cooked through." },
            { name: "Ginisang Labong", category: "Lunch", ingredients: "Labong, Shrimp/Pork", steps: "1. Sauté garlic and pork.\n2. Add bamboo shoots (labong).\n3. Season with patis and cook until tender." },
            { name: "Lechon Paksiw (Leftover)", category: "Lunch", ingredients: "Leftover Lechon, Mang Tomas", steps: "1. Put leftover lechon or fried pork in a pan.\n2. Pour Mang Tomas sauce and a little vinegar.\n3. Simmer until meat is very soft." },

            // Dinner Expansion (81-100)
            { name: "Beef Pares (Canned)", category: "Dinner", ingredients: "Canned Beef Pares, Bawang", steps: "1. Sauté lots of garlic.\n2. Add canned beef pares and heat up.\n3. Serve with a separate bowl of beef broth and garlic rice." },
            { name: "Grilled Liempo (Market)", category: "Dinner", ingredients: "Liempo, Soy Sauce", steps: "1. Marinate pork belly in soy sauce and calamansi.\n2. Grill over charcoal or pan-grill until charred and juicy." },
            { name: "Sinigang na Hipon", category: "Dinner", ingredients: "Hipon, Sinigang Mix, Labanos", steps: "1. Boil water with radish and sinigang mix.\n2. Add shrimp (hipon).\n3. Cook for only 3 mins so the shrimp stays succulent." },
            { name: "Nilagang Baka (Simple)", category: "Dinner", ingredients: "Beef, Patatas, Pechay", steps: "1. Boil beef for 1.5 hours until tender.\n2. Add potatoes and pechay.\n3. Season with salt and peppercorns." },
            { name: "Fried Eggplant w/ Soy Sauce", category: "Dinner", ingredients: "Talong, Toyo, Calamansi", steps: "1. Fry eggplant slices.\n2. Dip in a mixture of soy sauce and calamansi while hot." },
            { name: "Tuna Pasta (Spaghetti)", category: "Dinner", ingredients: "Canned Tuna, Spaghetti", steps: "1. Boil spaghetti.\n2. In a pan, sauté garlic, tuna, and mushrooms.\n3. Toss noodles in the tuna mixture." },
            { name: "Chicken Sopas", category: "Dinner", ingredients: "Macaroni, Chicken, Gatas", steps: "1. Boil chicken and macaroni together.\n2. Add carrots and evaporated milk.\n3. Season with salt and butter." },
            { name: "Fried Lumpia Togue", category: "Dinner", ingredients: "Lumpia Togue (Market)", steps: "1. Buy fresh lumpy togue from the market.\n2. Fry in high heat for a short time to keep the wrapper crispy and the veggies inside moist." },
            { name: "Chicken Inasal (Easy)", category: "Dinner", ingredients: "Chicken, Chicken Oil", steps: "1. Marinate chicken in ginger, garlic, and vinegar.\n2. Pan-fry and baste with chicken oil (achuete oil)." },
            { name: "Ginisang Sayote w/ Egg", category: "Dinner", ingredients: "Sayote, Itlog", steps: "1. Sauté sayote.\n2. Push veggies to the side and scramble an egg in the middle.\n3. Mix all together." },
            { name: "Adobong Pusit (Market)", category: "Dinner", ingredients: "Pusit, Toyo, Suka", steps: "1. Sauté squid quickly with garlic.\n2. Add soy sauce and vinegar. Cook until sauce is dark from the squid ink." },
            { name: "Pork Binagoongan", category: "Dinner", ingredients: "Pork, Bagoong Alamang", steps: "1. Sauté pork until brown.\n2. Add bagoong and a little sugar.\n3. Sauté until the bagoong smells cooked and covers the meat." },
            { name: "Fried Fish Ball / Kikiam", category: "Dinner", ingredients: "Fishballs, Sweet Sauce", steps: "1. Deep fry street-food style balls and kikiam.\n2. Make a sauce using starch, sugar, and soy sauce." },
            { name: "Sweet and Sour Meatballs", category: "Dinner", ingredients: "Meatballs (Frozen), Ketchup", steps: "1. Fry frozen meatballs.\n2. Sauté with a mix of ketchup, sugar, and water to make a quick sauce." },
            { name: "Ginisang Broccoli", category: "Dinner", ingredients: "Broccoli, Garlic, Oyster Sauce", steps: "1. Blach broccoli in boiling water for 1 min.\n2. Sauté with garlic and lots of oyster sauce." },
            { name: "Fried Chicken Wings", category: "Dinner", ingredients: "Chicken Wings, Flour", steps: "1. Coat wings in flour and salt.\n2. Deep fry until golden and crispy." },
            { name: "Instant Mami w/ Veggies", category: "Dinner", ingredients: "Instant Mami, Cabbage", steps: "1. Cook instant mami.\n2. Add a handful of shredded cabbage and a hard-boiled egg." },
            { name: "Tuna Salad (Quick)", category: "Dinner", ingredients: "Canned Tuna, Mayo, Onion", steps: "1. Mix tuna, mayo, and chopped onions.\n2. Serve cold or over hot rice." },
            { name: "Fried Spam w/ Garlic Rice", category: "Dinner", ingredients: "Spam, Sinangag", steps: "1. Fry spam slices.\n2. Serve with heavy garlic fried rice." },
            { name: "Ginisang Corned Beef w/ Potato", category: "Dinner", ingredients: "Corned Beef, Patatas", steps: "1. Fry potatoes first.\n2. Sauté corned beef with onions and mix the potatoes back in." },

            // Medium Dinner Expansion (101-150)
            { name: "Pork Steak", category: "Dinner", ingredients: "Pork Chop, Toyo, Calamansi, Sibuyas", steps: "1. Marinate pork in soy sauce and calamansi for 30 mins.\n2. Fry the pork chops and set aside.\n3. In the same pan, sauté lots of onion rings.\n4. Pour the marinade and simmer, then put back the pork." },
            { name: "Chicken Pastel", category: "Dinner", ingredients: "Manok, Mushroom, All-purpose Cream", steps: "1. Sauté chicken and mushrooms.\n2. Add water and simmer until chicken is soft.\n3. Pour all-purpose cream and stir until thick. Add cheese if available." },
            { name: "Beef Broccoli", category: "Dinner", ingredients: "Beef Strips, Broccoli, Oyster Sauce", steps: "1. Sauté beef strips until tender.\n2. Add broccoli florets.\n3. Pour oyster sauce and a little water. Cook for 2 mins." },
            { name: "Ginisang Munggo w/ Tinapa", category: "Dinner", ingredients: "Munggo, Tinapa Flakes, Malunggay", steps: "1. Boil munggo until it bursts.\n2. Sauté garlic, onions, and tinapa flakes.\n3. Add the munggo and malunggay. Simmer for 5 mins." },
            { name: "Sweet and Sour Fish", category: "Dinner", ingredients: "Isda, Ketchup, Suka, Pineapples", steps: "1. Fry the fish until golden.\n2. Make a sauce with ketchup, vinegar, and sugar.\n3. Sauté bell peppers and pineapple, then mix with the sauce and pour over fish." },
            { name: "Chicken Adobo w/ Pineapple", category: "Dinner", ingredients: "Manok, Pineapple Chunks, Toyo", steps: "1. Cook basic chicken adobo.\n2. Add pineapple chunks and juice 10 mins before finishing.\n3. Simmer until sauce is thick and sweet." },
            { name: "Pork Binagoongan w/ Talong", category: "Dinner", ingredients: "Pork, Bagoong, Talong", steps: "1. Fry eggplant slices and set aside.\n2. Sauté pork until brown and crispy.\n3. Add bagoong and simmer. Mix in the fried eggplant at the end." },
            { name: "Beef Tapa w/ Onions", category: "Dinner", ingredients: "Beef Tapa, Maraming Sibuyas", steps: "1. Fry beef tapa until done.\n2. Sauté 2 whole onions (sliced into rings) in the same pan until soft and sweet.\n3. Mix and serve." },
            { name: "Chicken Curry (Medium)", category: "Dinner", ingredients: "Manok, Curry Powder, Gata, Potato", steps: "1. Sauté chicken and potatoes.\n2. Add curry powder and stir.\n3. Add coconut milk and simmer until chicken is tender and sauce is yellow and thick." },
            { name: "Bicol Express (Dinner)", category: "Dinner", ingredients: "Pork, Gata, Sili, Bagoong", steps: "1. Sauté pork until brown.\n2. Add lots of green chili and bagoong.\n3. Simmer in coconut milk until the oil separates from the milk." },
            { name: "Ginisang Ampalaya w/ Beef Strips", category: "Dinner", ingredients: "Beef Strips, Ampalaya, Itlog", steps: "1. Sauté beef until brown.\n2. Add ampalaya and sauté.\n3. Pour beaten eggs and let set before stirring." },
            { name: "Pork Sinigang (Med Prep)", category: "Dinner", ingredients: "Pork, Sinigang Mix, Labanos", steps: "1. Boil pork with tomatoes and onions for 45 mins.\n2. Add sinigang mix and radish (labanos).\n3. Add kangkong or other greens at the very end." },
            { name: "Chicken Tinola (Med Prep)", category: "Dinner", ingredients: "Manok, Sayote, Dahon ng Sili", steps: "1. Sauté ginger and chicken.\n2. Add water and sayote. Simmer.\n3. Add chili leaves or malunggay and turn off heat immediately." },
            { name: "Beef Nilaga (Med Prep)", category: "Dinner", ingredients: "Beef, Patatas, Repolyo", steps: "1. Boil beef for at least 1 hour.\n2. Add potatoes and corn.\n3. Add cabbage last and season with plenty of black pepper." },
            { name: "Kare-Kare (Instant Mix Prep)", category: "Dinner", ingredients: "Pork/Beef, Kare-kare Mix, Peanut Butter", steps: "1. Boil meat until tender.\n2. Dissolve kare-kare mix in the broth.\n3. Add peanut butter for extra richness. Serve with sautéed veggies and bagoong." },
            { name: "Pork Kaldereta (Easy)", category: "Dinner", ingredients: "Pork, Tomato Sauce, Liver Spread", steps: "1. Sauté pork cubes.\n2. Add tomato sauce and liver spread.\n3. Add potatoes, carrots, and cheese. Simmer until thick." },
            { name: "Chicken Afritada (Classic)", category: "Dinner", ingredients: "Manok, Tomato Sauce, Carrots", steps: "1. Sauté chicken.\n2. Add tomato sauce and water.\n3. Add potatoes, carrots, and bell peppers. Simmer until veggies are soft." },
            { name: "Beef Mechado (Simple)", category: "Dinner", ingredients: "Beef, Tomato Sauce, Patatas", steps: "1. Sauté beef.\n2. Add tomato sauce and water.\n3. Simmer until beef is tender and sauce is thick and dark." },
            { name: "Fish Cardillo (Dinner)", category: "Dinner", ingredients: "Fried Isda, Itlog, Kamatis", steps: "1. Fry fish and set aside.\n2. Sauté garlic, onions, and lots of tomatoes.\n3. Add water and beaten eggs. Put the fish back in and simmer for 1 min." },
            { name: "Ginisang Sayote w/ Giniling", category: "Dinner", ingredients: "Sayote, Ground Pork, Bawang", steps: "1. Sauté ground pork.\n2. Add garlic and onions.\n3. Add sayote and cook until tender." },
            { name: "Pork Sisig w/ Mayo", category: "Dinner", ingredients: "Pork Belly, Sibuyas, Mayo, Sili", steps: "1. Fry pork belly until very crispy, then chop finely.\n2. Sauté with onions and chili.\n3. Add a large dollop of mayo and stir while hot." },
            { name: "Chicken Inasal (Home Style)", category: "Dinner", ingredients: "Manok, Atchuete, Calamansi", steps: "1. Marinate chicken in vinegar and calamansi.\n2. Fry in achuete oil to get that orange color.\n3. Serve with soy sauce, calamansi, and chili." },
            { name: "Beef Pepper Rice Style", category: "Dinner", ingredients: "Beef Strips, Mais, Butter, Kanin", steps: "1. Put rice in the middle of a hot pan.\n2. Place raw beef strips and corn around it.\n3. Add butter and lots of pepper. Mix everything together while cooking." },
            { name: "Baguio Beans w/ Corned Beef", category: "Dinner", ingredients: "Baguio Beans, Corned Beef", steps: "1. Sauté corned beef.\n2. Add baguio beans and sauté.\n3. Add a little water and simmer until beans are cooked." },
            { name: "Pork Teriyaki (Home)", category: "Dinner", ingredients: "Pork, Toyo, Asukal, Ginger", steps: "1. Sauté pork strips.\n2. Add soy sauce, sugar, and ginger.\n3. Simmer until the sauce becomes a thick glaze." },
            { name: "Chicken Teriyaki (Home)", category: "Dinner", ingredients: "Manok, Toyo, Asukal, Ginger", steps: "1. Sauté chicken pieces.\n2. Add soy sauce, sugar, and ginger.\n3. Simmer until the sauce becomes a thick glaze." },
            { name: "Togue w/ Pork Bits", category: "Dinner", ingredients: "Togue, Pork Bits, Tokwa", steps: "1. Sauté pork and tofu cubes.\n2. Add bean sprouts (togue).\n3. Sauté quickly so the sprouts stay crunchy." },
            { name: "Beef Stew (Market Prep)", category: "Dinner", ingredients: "Beef, Patatas, Tomato Paste", steps: "1. Sauté beef.\n2. Add tomato paste and water.\n3. Simmer for a long time until beef is very tender." },
            { name: "Chicken Sopas (Dinner Style)", category: "Dinner", ingredients: "Macaroni, Chicken, Evap Milk", steps: "1. Boil chicken and macaroni.\n2. Add hotdogs and carrots.\n3. Pour evaporated milk and butter for a rich dinner soup." },
            { name: "Pork Steak w/ Potato Cubes", category: "Dinner", ingredients: "Pork, Toyo, Calamansi, Patatas", steps: "1. Fry potatoes first.\n2. Fry marinated pork.\n3. Simmer both in the marinade with onion rings." },
            { name: "Fish Steak (Bangus Belly)", category: "Dinner", ingredients: "Bangus, Toyo, Calamansi, Sibuyas", steps: "1. Fry bangus belly.\n2. Make a steak sauce with soy sauce and calamansi.\n3. Pour over the fish and top with onion rings." },
            { name: "Pechay w/ Sardines (Dinner)", category: "Dinner", ingredients: "Pechay, Sardinas, Bawang", steps: "1. Sauté sardines.\n2. Add a lot of pechay and sauté briefly.\n3. Season with patis." },
            { name: "Pork Giniling w/ Quail Egg", category: "Dinner", ingredients: "Ground Pork, Itlog ng Pugo", steps: "1. Cook ground pork with potatoes.\n2. Add boiled quail eggs at the end.\n3. Simmer for 2 mins." },
            { name: "Chicken Mami (Home Prep)", category: "Dinner", ingredients: "Noodles, Chicken Strips, Egg", steps: "1. Boil chicken breast and shred.\n2. Use the broth for the noodles.\n3. Top with shredded chicken and a hard-boiled egg." },
            { name: "Beef Mami (Home Prep)", category: "Dinner", ingredients: "Noodles, Beef Strips, Egg", steps: "1. Boil beef until tender and slice into strips.\n2. Serve with noodles, broth, and a hard-boiled egg." },
            { name: "Repolyo w/ Ground Beef", category: "Dinner", ingredients: "Repolyo, Ground Beef", steps: "1. Sauté ground beef.\n2. Add shredded cabbage.\n3. Sauté until cabbage is tender." },
            { name: "Pork Chop w/ Gravy", category: "Dinner", ingredients: "Pork Chop, Mushroom Soup Mix", steps: "1. Fry pork chop.\n2. Make a thick gravy using mushroom soup mix.\n3. Pour over the pork chop." },
            { name: "Chicken w/ Gravy", category: "Dinner", ingredients: "Manok, Mushroom Soup Mix", steps: "1. Fry chicken.\n2. Make a thick gravy using mushroom soup mix.\n3. Pour over the chicken." },
            { name: "Patola w/ Shrimp (Dinner)", category: "Dinner", ingredients: "Patola, Hipon, Bawang", steps: "1. Sauté shrimp.\n2. Add patola slices and sauté.\n3. Add a little water and simmer." },
            { name: "Beef con Broccolli (Med)", category: "Dinner", ingredients: "Beef, Broccoli, Soy Sauce", steps: "1. Sauté beef strips.\n2. Add broccoli.\n3. Pour soy sauce and oyster sauce. Sauté quickly." },
            { name: "Pork and Beans w/ Hotdog", category: "Dinner", ingredients: "Canned Beans, Hotdog", steps: "1. Sauté hotdog slices.\n2. Add pork and beans.\n3. Heat until bubbling." },
            { name: "Kalabasa w/ Pork Bits", category: "Dinner", ingredients: "Kalabasa, Pork Bits, Sitaw", steps: "1. Sauté pork.\n2. Add squash and string beans.\n3. Simmer until veggies are soft." },
            { name: "Chicken Wings (Fried Only)", category: "Dinner", ingredients: "Chicken Wings, Breading", steps: "1. Coat wings in breading.\n2. Deep fry until very crispy." },
            { name: "Fish w/ Ginger Sauce", category: "Dinner", ingredients: "Fried Fish, Toyo, Ginger", steps: "1. Fry fish.\n2. Make a sauce with ginger, soy sauce, and sugar.\n3. Pour over the fish." },
            { name: "Beef Pares w/ Rice (Dinner)", category: "Dinner", ingredients: "Beef Pares (Canned), Sinangag", steps: "1. Heat canned beef pares.\n2. Serve with garlic rice and a side of clear soup." },
            { name: "Sayote w/ Sardines (Dinner)", category: "Dinner", ingredients: "Sayote, Sardinas, Bawang", steps: "1. Sauté sardines.\n2. Add sayote and cook until tender." },
            { name: "Pork Liempo Sinigang (Med)", category: "Dinner", ingredients: "Pork Liempo, Sinigang Mix", steps: "1. Boil pork liempo with sinigang mix.\n2. Add veggies and simmer until meat is soft." },
            { name: "Buffalo Chicken Wings", category: "Dinner", ingredients: "Chicken Wings, Hot Sauce", steps: "1. Fry wings.\n2. Toss in a mixture of hot sauce and melted butter." },
            { name: "Beef Stir-fry (Basic Med)", category: "Dinner", ingredients: "Beef, Bell Peppers, Toyo", steps: "1. Sauté beef and bell peppers.\n2. Season with soy sauce and pepper." },
            { name: "Sweet Corn w/ Crab Meat", category: "Dinner", ingredients: "Corn Soup Mix, Canned Crab", steps: "1. Cook corn soup mix.\n2. Add canned crab meat and an egg. Stir until thick." },

            // Hard/Special Recipes (151-160)
            { name: "Relyenong Bangus", category: "Dinner", ingredients: "Bangus, Raisins, Carrots, Itlog, Harina", steps: "1. Clean fish and remove scales.\n2. Pound the body to loosen meat, then squeeze out the meat through the neck.\n3. Boil meat and remove bones carefully.\n4. Sauté meat with carrots, raisins, and spices.\n5. Stuff the mixture back into the fish skin.\n6. Sew the opening, coat in flour, and fry until golden." },
            { name: "Kare-Kare (Ox Tail)", category: "Dinner", ingredients: "Ox Tail, Peanut Butter, Rice Flour, Bagoong", steps: "1. Boil ox tail for 3 hours until very tender.\n2. Toast rice flour in a pan until brown.\n3. Mix peanut butter and toasted flour with the broth to make a thick orange sauce.\n4. Add blanched veggies (pechay, sitaw, talong).\n5. Serve with sautéed bagoong." },
            { name: "Bulalo (Beef Shank)", category: "Dinner", ingredients: "Beef Shank (with marrow), Mais, Pechay, Patatas", steps: "1. Boil beef shanks in a large pot for 3-4 hours (remove scum regularly).\n2. Add whole peppercorns and onions.\n3. Add corn and potatoes when meat is starting to fall off the bone.\n4. Add pechay at the last minute." },
            { name: "Crispy Pata", category: "Dinner", ingredients: "Pork Leg (Whole), Vinegar, Garlic, Ginger", steps: "1. Boil pork leg with garlic, ginger, and salt for 1.5 hours.\n2. Let it cool and air-dry or put in the fridge for a few hours (essential for crispiness).\n3. Deep fry the whole leg in very hot oil (be careful of oil splatters!) until skin is golden and blistered." },
            { name: "Lechon Kawali (Special)", category: "Dinner", ingredients: "Pork Belly, Salt, Peppercorns, Oil", steps: "1. Boil pork belly with spices for 45 mins.\n2. Prick the skin with a fork and rub with salt.\n3. Dry in the fridge for at least 4 hours.\n4. Deep fry until skin is 'crackling' crispy." },
            { name: "Homemade Embutido", category: "Dinner", ingredients: "Ground Pork, Raisins, Cheese, Egg, Pickles", steps: "1. Mix ground pork, egg, cheese, raisins, and pickles.\n2. Shape into a log and wrap tightly in aluminum foil.\n3. Steam for 1 hour.\n4. Let cool, slice, and pan-fry before serving." },
            { name: "Special Pork Menudo", category: "Dinner", ingredients: "Pork, Liver, Potato, Carrots, Tomato Sauce", steps: "1. Sauté pork and liver.\n2. Add tomato sauce and water.\n3. Add potatoes, carrots, and raisins. Simmer until sauce is thick and oily." },
            { name: "Beef Caldereta (Special)", category: "Dinner", ingredients: "Beef, Liver Spread, Cheese, Bell Peppers", steps: "1. Sauté beef and simmer in tomato sauce until tender (at least 1.5 hours).\n2. Stir in liver spread and grated cheese to make the sauce creamy.\n3. Add potatoes, carrots, and bell peppers." },
            { name: "Relyenong Talong (Special)", category: "Dinner", ingredients: "Talong, Ground Pork, Egg, Onion", steps: "1. Grill eggplant and peel off skin.\n2. Sauté ground pork with onions and set aside.\n3. Flatten eggplant, top with sautéed pork.\n4. Dip the whole thing in beaten eggs and pan-fry carefully." },
            { name: "Pinakbet w/ Bagnet", category: "Dinner", ingredients: "Bagnet (Crispy Pork), Squash, Sitaw, Ampalaya, Bagoong", steps: "1. Prepare crispy bagnet (double fried pork belly).\n2. Cook traditional pinakbet veggies in bagoong.\n3. Top the veggies with bagnet just before serving to keep it crispy." },

            // Nakaka-gaan (Allowance Day) Dinner (161-170)
            { name: "Chicken Cordon Bleu Bites", category: "Dinner", ingredients: "Chicken Breast, Ham, Cheese, Breadcrumbs", steps: "1. Flatten chicken breast.\n2. Place ham and cheese inside and roll tightly.\n3. Secure with toothpick, coat in flour, egg, and breadcrumbs.\n4. Deep fry until golden. Slice into 'bites'." },
            { name: "Buttered Shrimp w/ Garlic", category: "Dinner", ingredients: "Shrimp, Butter, Garlic, Lemon-soda", steps: "1. Clean shrimp but keep the shell.\n2. Sauté lots of garlic in butter.\n3. Add shrimp and a splash of lemon-soda (Sprite).\n4. Simmer until sauce is thick and shrimp is orange." },
            { name: "Beef with Mushroom", category: "Dinner", ingredients: "Beef Strips, Canned Mushroom, Oyster Sauce", steps: "1. Sauté beef until tender.\n2. Add garlic and onions.\n3. Add sliced mushrooms and oyster sauce.\n4. Simmer for 2 mins." },
            { name: "Creamy Carbonara (Pinoy)", category: "Dinner", ingredients: "Pasta, Ham, All-purpose Cream, Cheese", steps: "1. Boil pasta.\n2. Sauté garlic and ham.\n3. Add cream and lots of cheese.\n4. Toss the pasta in the sauce." },
            { name: "Sweet and Sour Pork Chops", category: "Dinner", ingredients: "Pork Chops, Pineapple, Ketchup, Bell Pepper", steps: "1. Fry pork chops.\n2. Sauté veggies and pineapple.\n3. Add ketchup and sugar sauce mixture. Simmer until thick." },
            { name: "Sizzling Tofu (Resto Style)", category: "Dinner", ingredients: "Tofu, Mayo, Onions, Chili", steps: "1. Fry tofu cubes until very crispy.\n2. Mix with mayo, chopped onions, and chili.\n3. Serve on a hot plate if available." },
            { name: "Honey Garlic Chicken", category: "Dinner", ingredients: "Chicken Wings, Honey, Garlic, Soy Sauce", steps: "1. Fry chicken wings until crispy.\n2. Sauté garlic, add honey and soy sauce.\n3. Toss the wings in the glaze until coated." },
            { name: "Beef Bistek Special", category: "Dinner", ingredients: "Beef Slices, Soy Sauce, Calamansi, Onion Rings", steps: "1. Marinate beef.\n2. Fry beef and set aside.\n3. Sauté lots of onion rings, add marinade, and pour over the beef." },
            { name: "Chicken Teriyaki Thighs", category: "Dinner", ingredients: "Chicken Thighs, Soy Sauce, Sugar, Ginger", steps: "1. Sear chicken thighs skin-side down.\n2. Add teriyaki sauce (soy sauce, sugar, ginger).\n3. Simmer until the sauce is a thick glaze." },
            { name: "Ginisang Baguio Beans w/ Beef", category: "Dinner", ingredients: "Baguio Beans, Ground Beef, Garlic", steps: "1. Sauté ground beef until brown.\n2. Add garlic and Baguio beans.\n3. Season with soy sauce and stir-fry quickly." },

            // May Pera Ka Talaga (Treat Yourself) Dinner (171-180)
            { name: "Pan-Seared Ribeye Steak", category: "Dinner", ingredients: "Ribeye Steak, Butter, Garlic, Rosemary", steps: "1. Season steak with salt and pepper.\n2. Sear in a very hot pan with butter, garlic, and herbs.\n3. Baste with butter for 3-4 mins each side.\n4. Let it rest before slicing." },
            { name: "Garlic Butter Crab", category: "Dinner", ingredients: "Crab, Butter, Lots of Garlic, Parsley", steps: "1. Clean and steam the crabs.\n2. In a large pan, melt a whole block of butter and sauté 2 heads of garlic.\n3. Toss the crabs in the garlic butter until fully coated." },
            { name: "Grilled Salmon w/ Lemon Butter", category: "Dinner", ingredients: "Salmon Steak, Lemon, Butter, Garlic", steps: "1. Pan-fry or grill salmon steak.\n2. Make a sauce by melting butter with lemon juice and minced garlic.\n3. Pour over the salmon." },
            { name: "Baked Macaroni (Cheesy)", category: "Dinner", ingredients: "Macaroni, Meat Sauce, Mozzarella, Bechamel", steps: "1. Cook macaroni and mix with a rich meat sauce.\n2. Top with a creamy white sauce (bechamel) and lots of mozzarella cheese.\n3. Bake or torch until the cheese is bubbly and brown." },
            { name: "Special Seafood Sinigang", category: "Dinner", ingredients: "Shrimp, Squid, Fish, Mussels, Sinigang Mix", steps: "1. Boil water with sinigang mix and fresh tomatoes.\n2. Add various seafood one by one.\n3. Add premium veggies like okra and radish. Serve while steaming hot." },
            { name: "Creamy Beef Stroganoff", category: "Dinner", ingredients: "Beef Strips, Mushroom, Sour Cream/All-purpose Cream", steps: "1. Sauté beef in butter.\n2. Add onions and mushrooms.\n3. Stir in cream and season with lots of pepper. Serve over rice or pasta." },
            { name: "Stuffed Grilled Squid", category: "Dinner", ingredients: "Large Squid, Tomatoes, Onions, Ginger", steps: "1. Stuff the squid body with chopped tomatoes, onions, and ginger.\n2. Grill over charcoal until tender.\n3. Baste with a soy-sugar mixture." },
            { name: "Chicken Parmigiana", category: "Dinner", ingredients: "Chicken Fillet, Tomato Sauce, Cheese, Breadcrumbs", steps: "1. Fry breaded chicken fillet.\n2. Top with tomato sauce and mozzarella cheese.\n3. Melt the cheese in an oven or pan with a lid." },
            { name: "Braised Beef Short Ribs", category: "Dinner", ingredients: "Beef Ribs, Soy Sauce, Star Anise, Sugar", steps: "1. Sauté beef ribs.\n2. Simmer in a mixture of soy sauce, sugar, and star anise for 2 hours until the meat is literally melting off the bone." },
            { name: "Oven-style Roast Chicken", category: "Dinner", ingredients: "Whole Chicken, Butter, Lemon, Herbs", steps: "1. Rub whole chicken with butter, salt, and herbs.\n2. Stuff with lemon and garlic.\n3. Bake in an oven or air-fryer for 1 hour until the skin is golden and crispy." }
        ];

        let recipes = JSON.parse(localStorage.getItem('myRecipes'));
        
        // AUTO-SYNC: If user has old data (less than 180 recipes), force update to the new 180 list
        if (!recipes || recipes.length < starterRecipes.length) {
            recipes = starterRecipes;
            localStorage.setItem('myRecipes', JSON.stringify(recipes));
        }

        // History Buffer: Stores the last 10 items picked to avoid repeats
        let selectionHistory = { Breakfast: [], Lunch: [], Dinner: [] };
        const HISTORY_LIMIT = 10; 

        function resetRecipes() {
            if(confirm("Gusto mo bang i-reset ang listahan sa default (180 Recipes)? Mabubura ang mga dinagdag mo.")) {
                localStorage.removeItem('myRecipes');
                location.reload();
            }
        }

        function spin(category) {
            const filtered = recipes.filter(r => r.category === category);
            if (filtered.length === 0) return alert("Wala pang laman!");

            // Filter out items that are in the recent history
            let possibleMeals = filtered.filter(r => !selectionHistory[category].includes(r.name));
            
            // If we've run out of "new" things (rare with 180 recipes), clear history for this category
            if (possibleMeals.length === 0) {
                selectionHistory[category] = [];
                possibleMeals = filtered;
            }

            const mealDisplay = document.getElementById('meal-name');
            const instruction = document.getElementById('instruction-text');
            const recipeDetails = document.getElementById('recipe-details');
            const mealSteps = document.getElementById('meal-steps');
            
            instruction.classList.add('hidden');
            recipeDetails.classList.add('hidden');
            mealDisplay.classList.remove('hidden', 'text-yellow-400', 'pop-effect');
            mealDisplay.classList.add('slot-shuffle', 'text-white');

            let counter = 0;
            const maxSpins = 25; 
            
            const interval = setInterval(() => {
                const randomTemp = filtered[Math.floor(Math.random() * filtered.length)];
                mealDisplay.innerText = randomTemp.name.toUpperCase();
                counter++;

                if (counter >= maxSpins) {
                    clearInterval(interval);
                    
                    // Final Selection from filtered 'possibleMeals'
                    const finalMeal = possibleMeals[Math.floor(Math.random() * possibleMeals.length)];
                    
                    // CONFETTI BLAST! 🎊
                    confetti({
                        particleCount: 150,
                        spread: 70,
                        origin: { y: 0.6 },
                        colors: ['#f59e0b', '#ef4444', '#fbbf24', '#ffffff']
                    });

                    // Add to history and maintain limit
                    selectionHistory[category].push(finalMeal.name);
                    if (selectionHistory[category].length > HISTORY_LIMIT) {
                        selectionHistory[category].shift(); // Remove the oldest history item
                    }

                    mealDisplay.innerText = "✨ " + finalMeal.name.toUpperCase() + " ✨";
                    mealDisplay.classList.remove('slot-shuffle', 'text-white');
                    mealDisplay.classList.add('text-yellow-400', 'pop-effect');

                    setTimeout(() => {
                        document.getElementById('meal-ingredients').innerText = finalMeal.ingredients;
                        mealSteps.innerText = finalMeal.steps;
                        recipeDetails.classList.remove('hidden');
                    }, 300);
                }
            }, 80);
        }

        document.getElementById('recipe-form').addEventListener('submit', (e) => {
            e.preventDefault();
            const newR = {
                name: document.getElementById('form-name').value,
                category: document.getElementById('form-category').value,
                ingredients: document.getElementById('form-ingredients').value,
                steps: document.getElementById('form-steps').value
            };
            recipes.push(newR);
            localStorage.setItem('myRecipes', JSON.stringify(recipes));
            alert("Saved! Jackpot!");
            e.target.reset();
        });
    </script>
</body>
</html>

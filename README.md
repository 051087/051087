<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vestiaire Solidaire</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f8f8f8;
        }
        header {
            background-color: #333;
            color: white;
            padding: 1em 0;
            text-align: center;
        }
        nav a {
            margin: 0 15px;
            color: white;
            text-decoration: none;
        }
        main {
            padding: 2em;
        }
        form {
            display: flex;
            flex-direction: column;
        }
        label {
            margin-top: 1em;
        }
        button {
            margin-top: 1em;
            padding: 0.5em;
            background-color: #333;
            color: white;
            border: none;
            cursor: pointer;
        }
        .inventory-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 1em;
            background-color: #fff;
            border: 1px solid #ddd;
            margin-top: 1em;
        }
        .inventory-item button {
            background-color: red;
            padding: 0.2em 0.5em;
            margin-left: 1em;
        }
    </style>
</head>
<body>
    <header>
        <h1>Vestiaire Solidaire</h1>
        <nav>
            <a href="#home" id="nav-home">Accueil</a>
            <a href="#add" id="nav-add">Ajouter un article</a>
            <a href="#inventory" id="nav-inventory">Inventaire</a>
        </nav>
    </header>
    <main id="home" style="display:none;">
        <h2>Bienvenue au Vestiaire Solidaire</h2>
        <p>Gérez les dons de vêtements facilement.</p>
    </main>

    <main id="add" style="display:none;">
        <h1>Ajouter un article</h1>
        <form id="add-item-form">
            <label for="type">Type de vêtement :</label>
            <select id="type" name="type">
                <option value="pull">Pull</option>
                <option value="veste">Veste</option>
                <option value="t-shirt">T-shirt</option>
                <option value="pantalon">Pantalon</option>
                <option value="robe">Robe</option>
                <option value="jupe">Jupe</option>
                <option value="short">Short</option>
            </select>
            <label for="taille">Taille :</label>
            <input type="text" id="taille" name="taille" required>
            <label for="etat">État :</label>
            <input type="text" id="etat" name="etat" required>
            <button type="submit">Ajouter</button>
        </form>
    </main>

    <main id="inventory" style="display:none;">
        <h1>Inventaire</h1>
        <form id="filter-form">
            <label for="filter-type">Type de vêtement :</label>
            <select id="filter-type" name="filter-type">
                <option value="">Tous</option>
                <option value="pull">Pull</option>
                <option value="veste">Veste</option>
                <option value="t-shirt">T-shirt</option>
                <option value="pantalon">Pantalon</option>
                <option value="robe">Robe</option>
                <option value="jupe">Jupe</option>
                <option value="short">Short</option>
            </select>
            <label for="filter-taille">Taille :</label>
            <input type="text" id="filter-taille" name="filter-taille">
            <label for="filter-etat">État :</label>
            <input type="text" id="filter-etat" name="filter-etat">
            <button type="submit">Filtrer</button>
        </form>
        <div id="inventory-list"></div>
    </main>

    <main id="login">
        <h1>Connexion</h1>
        <form id="login-form">
            <label for="username">Nom d'utilisateur :</label>
            <input type="text" id="username" name="username" required>
            <label for="password">Mot de passe :</label>
            <input type="password" id="password" name="password" required>
            <button type="submit">Se connecter</button>
        </form>
    </main>

    <script>
        let isLoggedIn = false;

        // Fonction pour la navigation entre les sections
        document.querySelectorAll('nav a').forEach(link => {
            link.addEventListener('click', function(event) {
                event.preventDefault();
                if (!isLoggedIn) {
                    alert("Veuillez vous connecter d'abord.");
                    return;
                }
                document.querySelectorAll('main').forEach(section => {
                    section.style.display = 'none';
                });
                const targetId = this.getAttribute('href').substring(1);
                document.getElementById(targetId).style.display = 'block';
                if (targetId === 'inventory') {
                    loadInventory();
                }
            });
        });

        // Fonction pour charger l'inventaire depuis le localStorage
        function loadInventory() {
            const inventoryList = document.getElementById('inventory-list');
            inventoryList.innerHTML = '';
            const inventory = JSON.parse(localStorage.getItem('inventory')) || [];
            const filterType = document.getElementById('filter-type').value;
            const filterTaille = document.getElementById('filter-taille').value.toLowerCase();
            const filterEtat = document.getElementById('filter-etat').value.toLowerCase();

            const filteredInventory = inventory.filter(item => {
                return (filterType === '' || item.type === filterType) &&
                       (filterTaille === '' || item.taille.toLowerCase().includes(filterTaille)) &&
                       (filterEtat === '' || item.etat.toLowerCase().includes(filterEtat));
            });

            filteredInventory.forEach((item, index) => {
                const newItem = document.createElement('div');
                newItem.className = 'inventory-item';
                newItem.innerHTML = `Type: ${item.type}, Taille: ${item.taille}, État: ${item.etat} 
                    <button onclick="removeItem(${index})">Supprimer</button>`;
                inventoryList.appendChild(newItem);
            });
        }

        // Fonction pour ajouter un article à l'inventaire
        document.getElementById('add-item-form').addEventListener('submit', function(event) {
            event.preventDefault();
            const type = document.getElementById('type').value;
            const taille = document.getElementById('taille').value;
            const etat = document.getElementById('etat').value;

            const inventory = JSON.parse(localStorage.getItem('inventory')) || [];
            inventory.push({ type, taille, etat });
            localStorage.setItem('inventory', JSON.stringify(inventory));
            alert('Article ajouté à l\'inventaire!');
            document.getElementById('add-item-form').reset();
        });

        // Fonction pour supprimer un article de l'inventaire
        function removeItem(index) {
            const inventory = JSON.parse(localStorage.getItem('inventory')) || [];
            inventory.splice(index, 1);
            localStorage.setItem('inventory', JSON.stringify(inventory));
            loadInventory();
        }

        // Fonction pour gérer la connexion
        document.getElementById('login-form').addEventListener('submit', function(event) {
            event.preventDefault();
            const username = document.getElementById('username').value;
            const password = document.getElementById('password').value;

            // Vérification simple des identifiants (ajustez selon vos besoins)
            if (username === 'vestiaire solidaire' && password === 'vestiaire solidaire 2019051087') {
                isLoggedIn = true;
                // Afficher les sections après connexion
                document.querySelectorAll('main').forEach(section => {
                    section.style.display = 'none';
                });
                document.getElementById('home').style.display = 'block';
                document.querySelector('header').style.display = 'block';
            } else {
                alert('Nom d\'utilisateur ou mot de passe incorrect');
            }
        });

        // Masquer le header par défaut
        document.querySelector('header').style.display = 'none';

        // Afficher uniquement le formulaire de connexion par défaut
        document.querySelectorAll('main').forEach(section => {
            section.style.display = 'none';
        });
        document.getElementById('login').style.display = 'block';

        // Ajouter un écouteur d'événement au formulaire de filtre
        document.getElementById('filter-form').addEventListener('submit', function(event) {
            event.preventDefault();
            loadInventory();
        });
    </script>
</body>
</html>
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vestiaire Solidaire</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f8f8f8;
        }
        header {
            background-color: #333;
            color: white;
            padding: 1em 0;
            text-align: center;
        }
        nav a {
            margin: 0 15px;
            color: white;
            text-decoration: none;
        }
        main {
            padding: 2em;
        }
        form {
            display: flex;
            flex-direction: column;
        }
        label {
            margin-top: 1em;
        }
        button {
            margin-top: 1em;
            padding: 0.5em;
            background-color: #333;
            color: white;
            border: none;
            cursor: pointer;
        }
        .inventory-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 1em;
            background-color: #fff;
            border: 1px solid #ddd;
            margin-top: 1em;
        }
        .inventory-item button {
            background-color: red;
            padding: 0.2em 0.5em;
            margin-left: 1em;
        }
    </style>
</head>
<body>
    <header>
        <h1>Vestiaire Solidaire</h1>
        <nav>
            <a href="#home" id="nav-home">Accueil</a>
            <a href="#add" id="nav-add">Ajouter un article</a>
            <a href="#inventory" id="nav-inventory">Inventaire</a>
        </nav>
    </header>
    <main id="home" style="display:none;">
        <h2>Bienvenue au Vestiaire Solidaire</h2>
        <p>Gérez les dons de vêtements facilement.</p>
    </main>

    <main id="add" style="display:none;">
        <h1>Ajouter un article</h1>
        <form id="add-item-form">
            <label for="type">Type de vêtement :</label>
            <select id="type" name="type">
                <option value="pull">Pull</option>
                <option value="veste">Veste</option>
                <option value="t-shirt">T-shirt</option>
                <option value="pantalon">Pantalon</option>
                <option value="robe">Robe</option>
                <option value="jupe">Jupe</option>
                <option value="short">Short</option>
            </select>
            <label for="taille">Taille :</label>
            <input type="text" id="taille" name="taille" required>
            <label for="etat">État :</label>
            <input type="text" id="etat" name="etat" required>
            <button type="submit">Ajouter</button>
        </form>
    </main>

    <main id="inventory" style="display:none;">
        <h1>Inventaire</h1>
        <form id="filter-form">
            <label for="filter-type">Type de vêtement :</label>
            <select id="filter-type" name="filter-type">
                <option value="">Tous</option>
                <option value="pull">Pull</option>
                <option value="veste">Veste</option>
                <option value="t-shirt">T-shirt</option>
                <option value="pantalon">Pantalon</option>
                <option value="robe">Robe</option>
                <option value="jupe">Jupe</option>
                <option value="short">Short</option>
            </select>
            <label for="filter-taille">Taille :</label>
            <input type="text" id="filter-taille" name="filter-taille">
            <label for="filter-etat">État :</label>
            <input type="text" id="filter-etat" name="filter-etat">
            <button type="submit">Filtrer</button>
        </form>
        <div id="inventory-list"></div>
    </main>

    <main id="login">
        <h1>Connexion</h1>
        <form id="login-form">
            <label for="username">Nom d'utilisateur :</label>
            <input type="text" id="username" name="username" required>
            <label for="password">Mot de passe :</label>
            <input type="password" id="password" name="password" required>
            <button type="submit">Se connecter</button>
        </form>
    </main>

    <script>
        let isLoggedIn = false;

        // Fonction pour la navigation entre les sections
        document.querySelectorAll('nav a').forEach(link => {
            link.addEventListener('click', function(event) {
                event.preventDefault();
                if (!isLoggedIn) {
                    alert("Veuillez vous connecter d'abord.");
                    return;
                }
                document.querySelectorAll('main').forEach(section => {
                    section.style.display = 'none';
                });
                const targetId = this.getAttribute('href').substring(1);
                document.getElementById(targetId).style.display = 'block';
                if (targetId === 'inventory') {
                    loadInventory();
                }
            });
        });

        // Fonction pour charger l'inventaire depuis le localStorage
        function loadInventory() {
            const inventoryList = document.getElementById('inventory-list');
            inventoryList.innerHTML = '';
            const inventory = JSON.parse(localStorage.getItem('inventory')) || [];
            const filterType = document.getElementById('filter-type').value;
            const filterTaille = document.getElementById('filter-taille').value.toLowerCase();
            const filterEtat = document.getElementById('filter-etat').value.toLowerCase();

            const filteredInventory = inventory.filter(item => {
                return (filterType === '' || item.type === filterType) &&
                       (filterTaille === '' || item.taille.toLowerCase().includes(filterTaille)) &&
                       (filterEtat === '' || item.etat.toLowerCase().includes(filterEtat));
            });

            filteredInventory.forEach((item, index) => {
                const newItem = document.createElement('div');
                newItem.className = 'inventory-item';
                newItem.innerHTML = `Type: ${item.type}, Taille: ${item.taille}, État: ${item.etat} 
                    <button onclick="removeItem(${index})">Supprimer</button>`;
                inventoryList.appendChild(newItem);
            });
        }

        // Fonction pour ajouter un article à l'inventaire
        document.getElementById('add-item-form').addEventListener('submit', function(event) {
            event.preventDefault();
            const type = document.getElementById('type').value;
            const taille = document.getElementById('taille').value;
            const etat = document.getElementById('etat').value;

            const inventory = JSON.parse(localStorage.getItem('inventory')) || [];
            inventory.push({ type, taille, etat });
            localStorage.setItem('inventory', JSON.stringify(inventory));
            alert('Article ajouté à l\'inventaire!');
            document.getElementById('add-item-form').reset();
        });

        // Fonction pour supprimer un article de l'inventaire
        function removeItem(index) {
            const inventory = JSON.parse(localStorage.getItem('inventory')) || [];
            inventory.splice(index, 1);
            localStorage.setItem('inventory', JSON.stringify(inventory));
            loadInventory();
        }

        // Fonction pour gérer la connexion
        document.getElementById('login-form').addEventListener('submit', function(event) {
            event.preventDefault();
            const username = document.getElementById('username').value;
            const password = document.getElementById('password').value;

            // Vérification simple des identifiants (ajustez selon vos besoins)
            if (username === 'vestiaire solidaire' && password === 'vestiaire solidaire 2019051087') {
                isLoggedIn = true;
                // Afficher les sections après connexion
                document.querySelectorAll('main').forEach(section => {
                    section.style.display = 'none';
                });
                document.getElementById('home').style.display = 'block';
                document.querySelector('header').style.display = 'block';
            } else {
                alert('Nom d\'utilisateur ou mot de passe incorrect');
            }
        });

        // Masquer le header par défaut
        document.querySelector('header').style.display = 'none';

        // Afficher uniquement le formulaire de connexion par défaut
        document.querySelectorAll('main').forEach(section => {
            section.style.display = 'none';
        });
        document.getElementById('login').style.display = 'block';

        // Ajouter un écouteur d'événement au formulaire de filtre
        document.getElementById('filter-form').addEventListener('submit', function(event) {
            event.preventDefault();
            loadInventory();
        });
    </script>
</body>
</html>

---
marp: true
theme: default
paginate: true
size: 16:9
style: |
  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600&family=JetBrains+Mono:wght@400;700&display=swap');

  section {
    background-color: #f0f9ff; /* Sky 50 */
    color: #0c4a6e; /* Sky 900 */
    font-family: 'Inter', sans-serif;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }

  h1, h2, h3 {
    color: #0369a1; /* Sky 700 */
    font-weight: 600;
    letter-spacing: -0.025em;
    margin-bottom: 0.5rem;
  }

  h1 { font-size: 2.5rem; }
  h2 { font-size: 1.8rem; border-bottom: 2px solid #bae6fd; padding-bottom: 0.5rem; }

  code {
    font-family: 'JetBrains Mono', monospace;
    background-color: #e0f2fe; /* Sky 100 */
    color: #0369a1; /* Sky 700 */
    border: 1px solid #bae6fd; /* Sky 200 */
    border-radius: 4px;
    padding: 0.2em 0.4em;
    font-size: 0.9em;
  }

  pre {
    background-color: #ffffff;
    border: 1px solid #bae6fd; /* Sky 200 */
    border-radius: 8px;
    padding: 1rem;
    box-shadow: 0 4px 6px -1px rgba(14, 165, 233, 0.1);
  }

  pre code {
    background-color: transparent;
    border: none;
    padding: 0;
    color: #075985; /* Sky 800 */
  }

  .card {
    border: 1px solid #bae6fd; /* Sky 200 */
    background-color: #ffffff;
    padding: 1.5rem;
    border-radius: 8px;
    margin-top: 1rem;
    box-shadow: 0 2px 8px rgba(14, 165, 233, 0.08);
  }

  .accent {
    color: #0ea5e9; /* Sky 500 */
  }

  .footer {
    position: absolute;
    bottom: 20px;
    left: 40px;
    font-size: 0.8rem;
    color: #7dd3fc; /* Sky 300 */
  }

  section::after {
    color: #7dd3fc;
  }

  .logo-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    position: absolute;
    top: 30px;   
    left: 50px;
    right: 50px;
  }
  .logo-header img { 
    height: 110px; 
    filter: drop-shadow(0 4px 6px rgba(0,0,0,0.1)); 
    transition: transform 0.3s ease; 
  }
  .logo-header img:hover { transform: scale(1.05); }
---

<div class="logo-header">
  <img src="images/ofppt-logo.png" alt="OFPPT">
  <img src="images/logo-solicode.png" alt="Solicode">
</div>

# Validation des <span class="accent">Services</span> et <span class="accent">Données</span>

<br>

<div style="position: absolute; bottom: 80px; left: 40px; right: 40px; font-size: 1.1rem; color: #0369a1; display: flex; justify-content: space-between;">
  <div>
    <strong>Présenté par :</strong><br>
    Abdelhay Mallouli
  </div>
  <div style="text-align: right;">
    <strong>Encadré par :</strong><br>
    Monsieur Sarraj Fouad
  </div>
</div>

<div class="footer">Architecture logicielle & Tests automatisés</div>

---

## Sommaire

<div class="card" style="margin-top: 2rem; padding: 2rem;">
  <ul style="list-style-type: none; padding: 0; margin: 0; font-size: 1.2rem; line-height: 2;">
    <li><strong class="accent">1.</strong> Pourquoi Tester les Services ?</li>
    <li><strong class="accent">2.</strong> Le Rôle des Factories</li>
    <li><strong class="accent">3.</strong> Définition d'une Factory (Exemple Laravel)</li>
    <li><strong class="accent">4.</strong> Préparer les Données dans le Test</li>
    <li><strong class="accent">5.</strong> Valider la Logique du Service (Le Test)</li>
    <li><strong class="accent">6.</strong> Bonnes Pratiques : L'Approche Minimaliste</li>
  </ul>
</div>

<div class="footer">Architecture logicielle & Tests automatisés</div>

---

## 1. Pourquoi Tester les Services ?

Les **Services** contiennent le cœur de votre logique métier (business logic). Contrairement aux contrôleurs qui ne gèrent que le flux HTTP, les services effectuent les calculs, les vérifications et les enregistrements.

<div class="card">
<ul>
  <li><strong>Isolement :</strong> Vérifier la logique métier indépendamment de l'interface utilisateur ou des routes.</li>
  <li><strong>Fiabilité :</strong> S'assurer que des processus complexes (ex: facturation, création de profil de coaching) fonctionnent à 100%.</li>
  <li><strong>Refactoring sécurisé :</strong> Modifier le code interne du service sans risquer de casser le comportement attendu.</li>
</ul>
</div>

---

## 2. Le Rôle des Factories

Pour tester un service, il faut lui fournir des données valides. C'est ici qu'interviennent les **Factories**.

<div class="card" style="display: flex; gap: 20px;">
  <div style="flex: 1;">
    <h3 style="font-size: 1.2rem; color: #0ea5e9;">Approche classique (Manuelle)</h3>
    <p>Création d'objets lourde, répétitive et difficile à maintenir lors des changements de base de données.</p>
  </div>
  <div style="flex: 1; border-left: 2px solid #bae6fd; padding-left: 20px;">
    <h3 style="font-size: 1.2rem; color: #0ea5e9;">Approche Factory</h3>
    <p>Génération de données structurées et aléatoires à la volée. Code propre, minimaliste et réutilisable.</p>
  </div>
</div>

---

## 3. Définition d'une Factory (Exemple Laravel)

Une Factory permet de définir l'état par défaut d'un modèle (ex: un `Client` dans un système de gestion).

```php
namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

class ClientFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name'              => fake()->name(),
            'email'             => fake()->unique()->safeEmail(),
            'status'            => 'active',
            'subscription_plan' => 'premium',
        ];
    }
}
```

---

## 4. Préparer les Données dans le Test

Au lieu de créer les dépendances manuellement dans le test, nous utilisons la Factory pour générer un contexte maîtrisé en une seule ligne.

<div class="card">
<p><strong>Exemple de préparation (Arrange) :</strong></p>

```php
// Génère 3 clients inactifs en base de données de test
$clients = Client::factory()->count(3)->create([
    'status' => 'inactive'
]);

// Génère un client en mémoire (sans le sauvegarder en BDD)
$mockClient = Client::factory()->make();
```
</div>

---

## 5. Valider la Logique du Service (Le Test)

Testons un `SubscriptionService` qui s'assure qu'un client inactif peut être réactivé correctement.

```php
class SubscriptionServiceTest extends TestCase
{
    use RefreshDatabase; // Isolation stricte entre chaque test

    public function test_it_activates_a_client_subscription()
    {
        // 1. Arrange : Utilisation de la Factory
        $client  = Client::factory()->create(['status' => 'inactive']);
        $service = new SubscriptionService();

        // 2. Act : Exécution de la méthode du service
        $service->activateClient($client);

        // 3. Assert : Validation des données et de l'état
        $this->assertEquals('active', $client->fresh()->status);
        $this->assertDatabaseHas('action_logs', [
            'client_id' => $client->id,
            'action'    => 'activation',
        ]);
    }
}
```

---

## 6. Bonnes Pratiques : L'Approche Minimaliste

Pour des tests maintenables et une architecture robuste :

<div class="card">

1. **Une assertion par test (idéalement) :** Gardez vos tests courts. Si un test échoue, vous devez savoir exactement pourquoi sans relire 50 lignes de code.
2. **Utiliser des « States » dans les Factories :** Plutôt que de surcharger la méthode `create()`, définissez des états clairs dans la factory (ex: `->suspended()`, `->premium()`).
3. **Reset de la base de données :** Utilisez toujours le trait `RefreshDatabase` pour garantir un état propre entre chaque test (isolation stricte).

</div>

---

# <span class="accent">Conclusion</span>

Des services bien testés couplés à des factories robustes sont la fondation d'une application scalable.

<div style="margin-top: 2rem; color: #7dd3fc; font-family: 'JetBrains Mono', monospace; font-size: 0.9rem;">

> "Test behavior, not implementation."

</div>

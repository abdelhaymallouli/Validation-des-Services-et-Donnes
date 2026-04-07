---
marp: true
theme: default
paginate: true
size: 16:9
style: |
  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600&family=JetBrains+Mono:wght@400;700&display=swap');
  
  section {
    background-color: #09090b; /* Zinc 950 */
    color: #a1a1aa; /* Zinc 400 */
    font-family: 'Inter', sans-serif;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
  
  h1, h2, h3 {
    color: #fafafa; /* Zinc 50 */
    font-weight: 600;
    letter-spacing: -0.025em;
    margin-bottom: 0.5rem;
  }
  
  h1 { font-size: 2.5rem; }
  h2 { font-size: 1.8rem; border-bottom: 1px solid #27272a; padding-bottom: 0.5rem; }
  
  code {
    font-family: 'JetBrains Mono', monospace;
    background-color: #18181b; /* Zinc 900 */
    color: #e4e4e7;
    border: 1px solid #27272a; /* Zinc 800 */
    border-radius: 4px;
    padding: 0.2em 0.4em;
    font-size: 0.9em;
  }
  
  pre {
    background-color: #000000;
    border: 1px solid #27272a;
    border-radius: 8px;
    padding: 1rem;
    box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.5);
  }
  
  pre code {
    background-color: transparent;
    border: none;
    padding: 0;
    color: #cbd5e1;
  }
  
  .card {
    border: 1px solid #27272a;
    background-color: #111113;
    padding: 1.5rem;
    border-radius: 8px;
    margin-top: 1rem;
    box-shadow: inset 0 1px 0 rgba(255,255,255,0.05);
  }

  .accent {
    color: #38bdf8; /* Sky 400 */
  }
  
  .footer {
    position: absolute;
    bottom: 20px;
    left: 40px;
    font-size: 0.8rem;
    color: #52525b;
  }
---

# Tutoriel UA : Validation des <span class="accent">Services</span> et <span class="accent">DonnÃ©es</span>
<br>

<div class="card">
  <h3>MaÃ®triser les Tests Unitaires avec les Factories</h3>
  <p style="font-size: 1rem; margin-top: 10px;">Garantir l'intÃ©gritÃ© des donnÃ©es et la logique mÃ©tier dans un environnement moderne.</p>
</div>

<div class="footer">Architecture logicielle & Tests automatisÃ©s</div>

---

## 1. Pourquoi Tester les Services ?

Les **Services** contiennent le cÅ“ur de votre logique mÃ©tier (business logic). Contrairement aux contrÃ´leurs qui ne gÃ¨rent que le flux HTTP, les services effectuent les calculs, les vÃ©rifications et les enregistrements.

<div class="card">
<ul>
  <li><strong>Isolement :</strong> VÃ©rifier la logique mÃ©tier indÃ©pendamment de l'interface utilisateur ou des routes.</li>
  <li><strong>FiabilitÃ© :</strong> S'assurer que des processus complexes (ex: facturation, crÃ©ation de profil de coaching) fonctionnent Ã  100%.</li>
  <li><strong>Refactoring sÃ©curisÃ© :</strong> Modifier le code interne du service sans risquer de casser le comportement attendu.</li>
</ul>
</div>

---

## 2. Le RÃ´le des Factories

Pour tester un service, il faut lui fournir des donnÃ©es valides. C'est ici qu'interviennent les **Factories**.

<div class="card" style="display: flex; gap: 20px;">
  <div style="flex: 1;">
    <h3 style="font-size: 1.2rem; color: #38bdf8;">Approche classique (Manuelle)</h3>
    <p>CrÃ©ation d'objets lourde, rÃ©pÃ©titive et difficile Ã  maintenir lors des changements de base de donnÃ©es.</p>
  </div>
  <div style="flex: 1; border-left: 1px solid #27272a; padding-left: 20px;">
    <h3 style="font-size: 1.2rem; color: #38bdf8;">Approche Factory</h3>
    <p>GÃ©nÃ©ration de donnÃ©es structurÃ©es et alÃ©atoires Ã  la volÃ©e. Code propre, minimaliste et rÃ©utilisable.</p>
  </div>
</div>

---

## 3. DÃ©finition d'une Factory (Exemple Laravel)

Une Factory permet de dÃ©finir l'Ã©tat par dÃ©faut d'un modÃ¨le (ex: un `Client` dans un systÃ¨me de gestion).

```php
namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

class ClientFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->name(),
            'email' => fake()->unique()->safeEmail(),
            'status' => 'active',
            'subscription_plan' => 'premium',
        ];
    }
}
````

-----

## 4\. PrÃ©parer les DonnÃ©es dans le Test

Au lieu de crÃ©er les dÃ©pendances manuellement dans le test, nous utilisons la Factory pour gÃ©nÃ©rer un contexte maÃ®trisÃ© en une seule ligne.

\<div class="card"\>
\<p\>\<strong\>Exemple de prÃ©paration (Arrange) :\</strong\>\</p\>

```php
// GÃ©nÃ¨re 3 clients inactifs en base de donnÃ©es de test
$clients = Client::factory()->count(3)->create([
    'status' => 'inactive'
]);

// GÃ©nÃ¨re un client en mÃ©moire (sans le sauvegarder en BDD)
$mockClient = Client::factory()->make();
```

\</div\>

-----

## 5\. Valider la Logique du Service (Le Test)

Testons un `SubscriptionService` qui s'assure qu'un client inactif peut Ãªtre rÃ©activÃ© correctement.

```php
public function test_it_activates_a_client_subscription()
{
    // 1. Arrange : Utilisation de la Factory
    $client = Client::factory()->create(['status' => 'inactive']);
    $service = new SubscriptionService();

    // 2. Act : ExÃ©cution de la mÃ©thode du service
    $service->activateClient($client);

    // 3. Assert : Validation des donnÃ©es et de l'Ã©tat
    $this->assertEquals('active', $client->fresh()->status);
    $this->assertDatabaseHas('action_logs', [
        'client_id' => $client->id,
        'action' => 'activation'
    ]);
}
```

-----

## 6\. Bonnes Pratiques : L'Approche Minimaliste

Pour des tests maintenables et une architecture robuste :

\<div class="card"\>

1.  **Une assertion par test (idÃ©alement) :** Gardez vos tests courts. Si un test Ã©choue, vous devez savoir exactement pourquoi sans relire 50 lignes de code.
2.  **Utiliser des "States" dans les Factories :** PlutÃ´t que de surcharger la mÃ©thode `create()`, dÃ©finissez des Ã©tats clairs dans la factory (ex: `->suspended()`, `->premium()`).
3.  **Reset de la base de donnÃ©es :** Utilisez toujours le trait `RefreshDatabase` (ou Ã©quivalent) pour garantir un Ã©tat propre (isolation stricte).

\</div\>

-----

# \<span class="accent"\>Conclusion\</span\>

Des services bien testÃ©s couplÃ©s Ã  des factories robustes sont la fondation d'une application scalable.

\<div style="margin-top: 2rem; color: \#52525b; font-family: 'JetBrains Mono', monospace; font-size: 0.9rem;"\>
\> "Test behavior, not implementation."
\</div\>

```

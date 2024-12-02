
### **Documentation complète : Création d’un projet en C# avec POO**

----------

## **1. Introduction à la Programmation Orientée Objet (POO)**

La POO est une méthode de programmation qui structure le code en regroupant les données (**attributs**) et les actions (**méthodes**) dans des **objets**. C# est un langage orienté objet, conçu pour simplifier le développement de projets robustes et maintenables. Les concepts clés sont :

-   **Classes et Objets** : Une classe est un modèle (ou blueprint) ; un objet est une instance de cette classe.
-   **Encapsulation** : Les données sont protégées par des modificateurs d’accès (`private`, `public`, etc.).
-   **Héritage** : Une classe peut hériter des fonctionnalités d’une autre.
-   **Polymorphisme** : Une méthode peut se comporter différemment selon le contexte.
-   **Abstraction** : Isoler les détails complexes pour ne montrer que l’essentiel.

----------

## **2. Structure d’un projet C#**

Un projet C# suit une structure organisée en plusieurs fichiers pour faciliter la maintenance et la compréhension. Voici un exemple pour notre projet :

### **Organisation des fichiers**

```scss
CarProject/
│
├── Program.cs         (Point d'entrée : méthode Main)
├── Models/
│   ├── Car.cs         (Classe principale : Voiture)
│   ├── ElectricCar.cs (Classe dérivée : Voiture électrique)
│   ├── Fleet.cs       (Gestionnaire de parc automobile)
├── Services/
│   ├── FleetFileManager.cs (Gestionnaire de fichiers)
├── Interfaces/
│   ├── IInsurable.cs  (Interface pour assurer les voitures)` 
```
----------

## **3. Configuration et création du projet**

1.  **Créer le projet :**
    -   Ouvre Visual Studio ou un éditeur comme Visual Studio Code.
    -   Crée un projet C# Console :
```bash
dotnet new console -n CarProject
cd CarProject
```        
2.  **Ajoute les dossiers pour l'organisation :**
    -   Crée les dossiers `Models`, `Services`, et `Interfaces`.

----------

## **4. Implémentation des classes et interfaces**

### **Classe principale : `Car`**

Elle représente une voiture générique avec ses propriétés et comportements.


```csharp
namespace CarProject.Models
{
    public class Car
    {
        public string Brand { get; set; }
        public string Model { get; set; }
        public double Speed { get; private set; }
        public double Fuel { get; private set; }
        public double FuelCapacity { get; private set; }

        public Car(string brand, string model, double fuelCapacity)
        {
            Brand = brand;
            Model = model;
            Speed = 0;
            Fuel = fuelCapacity;
            FuelCapacity = fuelCapacity;
        }

        public void Accelerate(double increment)
        {
            if (Fuel > 0)
            {
                Speed += increment;
                Fuel -= increment * 0.1;
                Console.WriteLine($"{Brand} {Model} accélère à {Speed} km/h. Carburant restant : {Fuel:F2} L.");
            }
            else
            {
                Console.WriteLine($"{Brand} {Model} n'a plus de carburant pour accélérer.");
            }
        }

        public void Brake(double decrement)
        {
            Speed = Math.Max(Speed - decrement, 0);
            Console.WriteLine($"{Brand} {Model} freine. Vitesse actuelle : {Speed} km/h.");
        }

        public void Refuel(double amount)
        {
            Fuel = Math.Min(Fuel + amount, FuelCapacity);
            Console.WriteLine($"{Brand} {Model} fait le plein. Carburant : {Fuel:F2} L.");
        }
    }
}
```

----------

### **Classe dérivée : `ElectricCar`**

Une voiture électrique hérite des attributs de **Car**, mais ajoute des fonctionnalités spécifiques comme la gestion de la batterie.

```csharp
namespace CarProject.Models
{
    public class ElectricCar : Car
    {
        public double BatteryLevel { get; private set; }

        public ElectricCar(string brand, string model, double batteryLevel)
            : base(brand, model, 0)
        {
            BatteryLevel = batteryLevel;
        }

        public void Charge(double amount)
        {
            BatteryLevel = Math.Min(BatteryLevel + amount, 100);
            Console.WriteLine($"{Brand} {Model} charge sa batterie. Niveau : {BatteryLevel}%.");
        }

        public override void Accelerate(double increment)
        {
            if (BatteryLevel > 0)
            {
                base.Accelerate(increment);
                BatteryLevel -= increment * 0.5;
                Console.WriteLine($"Niveau de batterie : {BatteryLevel}%.");
            }
            else
            {
                Console.WriteLine($"{Brand} {Model} n'a plus de batterie pour accélérer.");
            }
        }
    }
}
```

----------

### **Classe `Fleet` : Gestion d’un parc automobile**

Elle gère une collection de voitures (type `Car` ou ses dérivés).

```csharp
using System;
using System.Collections.Generic;

namespace CarProject.Models
{
    public class Fleet
    {
        private List<Car> cars = new List<Car>();

        public void AddCar(Car car)
        {
            cars.Add(car);
            Console.WriteLine($"Ajouté : {car.Brand} {car.Model} au parc.");
        }

        public void DisplayAllCars()
        {
            Console.WriteLine("Liste des voitures dans le parc :");
            foreach (var car in cars)
            {
                Console.WriteLine($"- {car.Brand} {car.Model}");
            }
        }
    }
}
```

----------

### **Gestionnaire de fichiers : `FleetFileManager`**

Sauvegarde et charge les données des voitures.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using CarProject.Models;

namespace CarProject.Services
{
    public class FleetFileManager
    {
        public void SaveFleet(List<Car> fleet, string filePath)
        {
            using (StreamWriter writer = new StreamWriter(filePath))
            {
                foreach (var car in fleet)
                {
                    writer.WriteLine($"{car.Brand},{car.Model},{car.Fuel}");
                }
            }
        }

        public List<Car> LoadFleet(string filePath)
        {
            List<Car> fleet = new List<Car>();
            using (StreamReader reader = new StreamReader(filePath))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    var parts = line.Split(',');
                    fleet.Add(new Car(parts[0], parts[1], double.Parse(parts[2])));
                }
            }
            return fleet;
        }
    }
}
```

----------

## **5. Fonction principale : `Main`**

La méthode `Main` est le point d'entrée de l'application. Elle utilise toutes les classes et fonctionnalités créées.


```csharp
using System;
using CarProject.Models;
using CarProject.Services;

namespace CarProject
{
    class Program
    {
        static void Main(string[] args)
        {
            // Création d'un parc automobile
            Fleet fleet = new Fleet();

            // Ajout de voitures
            fleet.AddCar(new Car("Toyota", "Corolla", 50));
            fleet.AddCar(new ElectricCar("Tesla", "Model 3", 80));

            // Afficher les voitures
            fleet.DisplayAllCars();

            // Tester les fonctionnalités d'une voiture
            Car car = new Car("Ford", "Fiesta", 45);
            car.Accelerate(20);
            car.Brake(10);
            car.Refuel(15);

            // Tester une voiture électrique
            ElectricCar tesla = new ElectricCar("Tesla", "Model S", 90);
            tesla.Accelerate(30);
            tesla.Charge(20);

            // Sauvegarder et charger le parc automobile
            FleetFileManager fileManager = new FleetFileManager();
            fileManager.SaveFleet(new List<Car> { car, tesla }, "fleet.txt");
            var loadedFleet = fileManager.LoadFleet("fleet.txt");

            Console.WriteLine("Chargement depuis le fichier :");
            foreach (var loadedCar in loadedFleet)
            {
                Console.WriteLine($"{loadedCar.Brand} {loadedCar.Model}");
            }
        }
    }
}
```

----------

## **6. Étapes finales**

1.  **Compilation et exécution :**
    -   Dans le terminal :
```bash
dotnet build
dotnet run
```
        
2.  **Exploration :**
    -   Modifie les classes ou ajoute de nouvelles fonctionnalités (comme des événements ou des interfaces supplémentaires).

Avec cette documentation, tu as un guide complet pour créer un projet C# orienté objet structuré et fonctionnel ! 🎉 Si tu veux approfondir un sujet ou ajouter des fonctionnalités, fais-le-moi savoir. 😊




### **Approfondissement de la Programmation Orientée Objet (POO) en C# : Héritage, Interfaces, Polymorphisme, etc.**

Dans cette section, nous allons explorer les concepts avancés de la POO en C#, notamment l’héritage, les interfaces, l’abstraction, et le polymorphisme. Chaque notion sera expliquée avec des exemples appliqués à notre projet de gestion de voitures.

----------

## **1. Héritage : Spécialiser des classes**

L’héritage permet de créer une classe dérivée à partir d'une classe de base, en réutilisant et étendant ses fonctionnalités.

### Exemple avec les voitures

#### **Classe de base : `Car`**

Elle contient des attributs et méthodes de base comme `Brand`, `Model`, et `Accelerate`.

```csharp
public class Car
{
    public string Brand { get; set; }
    public string Model { get; set; }
    public double Speed { get; private set; }
    public double Fuel { get; private set; }

    public Car(string brand, string model, double fuel)
    {
        Brand = brand;
        Model = model;
        Speed = 0;
        Fuel = fuel;
    }

    public void Accelerate(double increment)
    {
        if (Fuel > 0)
        {
            Speed += increment;
            Fuel -= increment * 0.1;
            Console.WriteLine($"{Brand} {Model} accélère à {Speed} km/h.");
        }
        else
        {
            Console.WriteLine($"{Brand} {Model} n'a plus de carburant.");
        }
    }
}
```

#### **Classe dérivée : `ElectricCar`**

Elle hérite de `Car` mais ajoute des fonctionnalités spécifiques comme la gestion de la batterie.


```csharp
public class ElectricCar : Car
{
    public double BatteryLevel { get; private set; } // Nouveau champ

    public ElectricCar(string brand, string model, double batteryLevel)
        : base(brand, model, 0) // Appel au constructeur de la classe de base
    {
        BatteryLevel = batteryLevel;
    }

    public void Charge(double amount)
    {
        BatteryLevel = Math.Min(BatteryLevel + amount, 100);
        Console.WriteLine($"{Brand} {Model} recharge sa batterie à {BatteryLevel}%.");
    }

    // Redéfinition (override) d'une méthode
    public override void Accelerate(double increment)
    {
        if (BatteryLevel > 0)
        {
            base.Accelerate(increment); // Appelle la méthode de la classe de base
            BatteryLevel -= increment * 0.2;
            Console.WriteLine($"Batterie restante : {BatteryLevel}%.");
        }
        else
        {
            Console.WriteLine($"{Brand} {Model} n'a plus de batterie.");
        }
    }
}
```

**Points clés :**

1.  La méthode `Charge` est spécifique à `ElectricCar`.
2.  La méthode `Accelerate` est redéfinie avec le mot-clé `override`.

**Utilisation :**


```csharp
ElectricCar tesla = new ElectricCar("Tesla", "Model 3", 80);
tesla.Accelerate(20); // Utilise la méthode redéfinie
tesla.Charge(10);     // Fonctionnalité spécifique` 
```
----------

## **2. Interfaces : Contrats pour les classes**

Une interface définit un contrat que les classes doivent respecter. Elle ne contient que des déclarations de méthodes ou propriétés, sans implémentation.

### Définir une interface

Créons une interface pour ajouter des capacités d'assurance aux voitures.


```csharp
public interface IInsurable
{
    string InsuranceProvider { get; set; } // Propriété
    void GetInsuranceDetails(); // Méthode
}
```

### Implémenter l’interface

Une classe peut implémenter plusieurs interfaces en plus d'hériter d'une classe.


```csharp
public class InsurableCar : Car, IInsurable
{
    public string InsuranceProvider { get; set; } // Implémentation de l'interface

    public InsurableCar(string brand, string model, double fuel, string insuranceProvider)
        : base(brand, model, fuel)
    {
        InsuranceProvider = insuranceProvider;
    }

    public void GetInsuranceDetails()
    {
        Console.WriteLine($"{Brand} {Model} est assuré chez {InsuranceProvider}.");
    }
}
```

**Utilisation :**


```csharp
InsurableCar car = new InsurableCar("BMW", "X5", 50, "AXA");
car.GetInsuranceDetails(); // Affiche les détails de l'assurance
```

### Pourquoi utiliser des interfaces ?

1.  Permettent de **standardiser** les comportements.
2.  Une classe peut implémenter **plusieurs interfaces**, mais ne peut hériter que d'une seule classe.

----------

## **3. Polymorphisme : Comportement multiple**

Le polymorphisme permet à une méthode ou un objet de se comporter différemment selon le contexte.

### Polymorphisme par redéfinition

Avec le mot-clé `override`, une méthode dans une classe dérivée remplace celle de la classe de base.


```csharp
public class SportCar : Car
{
    public SportCar(string brand, string model, double fuel) 
        : base(brand, model, fuel) { }

    public override void Accelerate(double increment)
    {
        base.Accelerate(increment * 2); // La voiture de sport accélère plus vite
        Console.WriteLine($"{Brand} {Model} est une voiture de sport !");
    }
}
```

### Polymorphisme par substitution

Une instance d’une classe dérivée peut être traitée comme une instance de la classe de base.


```csharp
Car myCar = new SportCar("Ferrari", "488", 60); // Type de base
myCar.Accelerate(10); // Utilise la version redéfinie
```

**Points clés :**

-   Le type déclaré (`Car`) détermine quelles méthodes sont disponibles.
-   Le type réel (`SportCar`) détermine quelle version est exécutée.

----------

## **4. Abstraction : Cacher les détails**

### Classes abstraites

Une classe abstraite ne peut pas être instanciée directement. Elle sert de base pour d'autres classes.


```csharp
public abstract class Vehicle
{
    public string Brand { get; set; }
    public string Model { get; set; }

    public Vehicle(string brand, string model)
    {
        Brand = brand;
        Model = model;
    }

    public abstract void Start(); // Méthode abstraite
}
```

### Implémenter une classe abstraite

Les classes dérivées doivent fournir une implémentation des méthodes abstraites.


```csharp
public class GasCar : Vehicle
{
    public GasCar(string brand, string model) : base(brand, model) { }

    public override void Start()
    {
        Console.WriteLine($"{Brand} {Model} démarre avec un moteur à essence.");
    }
}
```

**Utilisation :**


```csharp
GasCar car = new GasCar("Toyota", "Yaris");
car.Start(); // Appelle la méthode implémentée
```

----------

## **5. Synthèse dans `Main`**

Voici un exemple utilisant tous les concepts expliqués :


```csharp
using System;
using CarProject.Models;

class Program
{
    static void Main(string[] args)
    {
        // Héritage
        Car genericCar = new Car("Toyota", "Corolla", 50);
        ElectricCar tesla = new ElectricCar("Tesla", "Model 3", 80);
        SportCar ferrari = new SportCar("Ferrari", "488", 70);

        // Interface
        InsurableCar insuredCar = new InsurableCar("BMW", "X5", 50, "AXA");

        // Polymorphisme
        Car polymorphicCar = new SportCar("Lamborghini", "Huracan", 80);

        // Utilisation
        genericCar.Accelerate(10);
        tesla.Accelerate(20);
        tesla.Charge(15);
        insuredCar.GetInsuranceDetails();
        polymorphicCar.Accelerate(15);

        // Liste polymorphique
        var fleet = new List<Car> { genericCar, tesla, ferrari, polymorphicCar };
        foreach (var car in fleet)
        {
            Console.WriteLine($"- {car.Brand} {car.Model}");
        }
    }
}
```

----------

Avec cette partie, tu comprends comment utiliser l’héritage, les interfaces et le polymorphisme pour rendre ton code plus structuré, réutilisable et extensible. Si tu veux encore plus d'exemples ou des exercices pratiques, dis-le-moi ! 🚀

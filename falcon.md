# SpaceX - Falcon 9

## Függőleges landolás rakéta meghajtással

### Motiváció
- Miért jobb, mint pl. repülőgép üzemmódban?
- [Falcon 9 függőleges fel és leszállás teszt](https://www.youtube.com/watch?v=ZwwS4YOTbbw). Ők is így kezdték.
- [Falcon 9 rakéta első fokozazának visszatérése](https://www.youtube.com/watch?v=ANv5UfZsvZQ)
- [jan 18-ai sikertelen visszatérés egy úszó platformra](https://www.youtube.com/watch?v=Nd9ySTUVEMA)

### Pálya létrehozása

- Plane, 30x30 méter, Standard Assets / Environment / Terrain Asset-ből textúra
- [Falcon 9 model](https://github.com/zlsa/f9r-v2), blend fájl importálható közvetlenül
- Rakétára: Standard Assets / ParticleSystems / Prefabs / Afterburner
- AfterBurner + Falcon 9 összefogása közös Empty GameObject-be
  - Rigibody, Capsule Collider
- Standard Assets / Cameras / MultiPurposeCameraRig
  - Kamera kövesse a rakétát (Player tag-gel automatikus)
  - Pivot / Y: követett objektum fölötti magasság
  - MainCamera / Z: követett objektumtól való távolság (pl. -100 a falcon modellhez)
  
### Irányítás

- Egyszerű irányítás: Space bekapcsolja a motort, y irányú erőhatással
  - Ez az AfterBurner particleSystem-et is kapcsolja ki/be
```csharp
        ParticleSystem.EmissionModule emission = afterBurner.emission;
        emission.enabled = ???;         
```
- Nem megy a motor -> csak a gravitáció dolgozik
- Legyen állítható a motor hajtó ereje
	
### Státusz kijelzés

- Egy text objektumra írjuk ki az aktuális magasságot és sebességet	
	
### Földet érés sebessége
- A síkkal való ütközés
```csharp
    void OnCollisionEnter(Collision collision)
    {
        touchdown = "Földet érés: " + collision.relativeVelocity.magnitude;
    }

    void OnCollisionExit(Collision collision)
    {
        touchdown = "";
    }
```

### Játék
- Minél gyorsabban kell leszállni (mérje az időt)
- Sikeres landolás: adott maximális sebesség alatt érjen földet
- Túl nagy sebességnél dőljön el / robbanjon fel a rakéta

### Kihívás:
- Automatikus landolás adott max földetérési sebességgel, adott motor hajtóerő mellett, tetszőleges magasságból csak a motor ki/be kapcsolásával
- Verseny: 2 rakéta, kinek az algoritmusa landol gyorsabban (úgy hogy azért nem töri össze a rakétát)
  - Esetleg játék az automata ellen (bár ha jó az algoritmus, akkor ez nem egy sikerélményekkel teli játék)

![Image](falcon.png?raw=true)

# Info szakkör

## Unity ismerkedés

### Pálya létrehozása
- Unity elindítása, új projekt létrehozása
- Játékterület kialakítása
  - Gömb létrehozása (`Game Object > 3D Object > Sphere`, vagy Hierarchy view-ban `jobb kattintás > 3D Object > Sphere`)
  - Inspector nézet áttekintése
  - Transform: Pozíció, elforgatás, skálázás  
  - Komponensek, ki/be kapcsolás
  - Fizika, Collider, Rigidbody áttekintése
  - Fizikai interakciókhoz adjunk hozzá egy Rigidbody components (`Add component > Physics > Rigidbody`)
    - Play gombbal próbáljuk ki
  - Tegyünk alá valamit, hogy ne essen le
    - Sík: `Game Object > 3D Object > Plane`
    - Collider koncepciója
    - Alapból van a síkon Collider: mi történik, ha kikapcsoljuk? 

### Irányítás  
- Az objektumokat pozícionálhatjuk közvetlenül (`Rigidbody > Is Kinematic`) vagy a fizikai motort használva erőhatásokkal
- `Add component > New script`, nevezzük el: PlayerController 
  - Ez létrehoz egy C# forrás fájlt, nyissuk meg
  - Forrás fájl áttekintése, Unity callback életciklus röviden
```csharp
using UnityEngine;

public class PlayerController : MonoBehaviour {

    public float speed = 5;

    private Rigidbody rb;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }

    void FixedUpdate()
    {
        float moveHorizontal = Input.GetAxis("Horizontal");
        float moveVertical = Input.GetAxis("Vertical");

        Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);

        rb.AddForce(movement * speed);
    } 
}
```
### Tárgyak felszedése
- Rakjunk fel egy kockát
- Rigidbody / Is Kinematic -ot kapcsoljuk be, később majd kódból mozgathatjuk
- Collider / Is Trigger -t kapcsoljuk be, hogy értesüljünk az ütközésről, ne pedig lepattanjunk róla
- Rakjunk rá egy tag-et (pl. "Felszed")
- A PlayerControllerben detektáljuk az ütközést:
```csharp
void OnTriggerEnter(Collider other) 
{
    if (other.gameObject.CompareTag ("Felszed"))
    {
        other.gameObject.SetActive (false);
    }
}
```    

### Pontok kijelzése
- Rakjunk fel egy szöveg dobozt: `Game Object > UI > Text`
- A PlayerControllerben hasznaljuk majd:
  - UI namespace
```csharp
using UnityEngine.UI;
```
  - Referencia a szövegdobozra, amire majd kiírunk, ill. számoljuk a felszedett tárgyakat
```csharp
public Text pontKijelzo;
private int pontok;
```
  - Ha felszedünk valamit, növeljük a számlálót, ill. frissítjük a szöveget
```csharp
pontok++;
pontKijelzo.text = "Pontok: " + pontok;
```
- Ne felejtsük a referenciát beállítani a tényleges szövegdobozra

### Házi feladat: Föld
- Sima gömb unalmas, tegyünk rá Föld textúrát
- Pl.: http://planetpixelemporium.com/earth.html

### Házi feladat: adjunk széleket a pályához
- Azért, hogy ne essünk le

### Házi feladat: ugrálás
- Legyen állítható az ugrás erősség
```csharp
public float jumpPower = 1;
```
- "Ugrás" gomb le van nyomva?
```csharp
bool jump = Input.GetButton("Jump");
```
- Ha igen, akkor adjunk egy lökést az objektumnak
```csharp
rb.AddForce(Vector3.up * jumpPower, ForceMode.Impulse);
```

### Házi feladat: Győzelem
- Rakjunk ki valamilyen mókás feliratot, ha minden tárgyat felszedett a játékos
- Számoljuk össze kódból a felszedendő objektumokat:
```csharp
max = GameObject.FindGameObjectsWithTag("Felszed").Length;
```

### Házi feladat (haladó): Félős céltárgy
- Csináljunk olyan szkriptet, amit ha ráteszünk a célobjektumra, a következőt csinálja:
  - Figyeli, hogy közel van-e a játékos
  - Ha igen, összemegy kicsire (vagy arréb ugrik pl.)
  - Ha nem, akkor visszaáll eredeti méretére
  - Ehhez kell egy olyan Trigger Collider, ami nagyobb, mint amit a felszedéshez használunk
    - Új Collidert csak egy új GameObject-re tehetünk, célszerű tehát egy "Empty" objektumba tenni a kockát, és egy másik empty-t ami figyeli a játékos közelségét
```csharp
using UnityEngine;

public class Félős : MonoBehaviour
{
    public float osszehuzodas = 0.5f;

    public Transform target;
    private Vector3 eredetiMeret;

    void Start()
    {
        eredetiMeret = target.localScale;
    }

    void OnTriggerEnter(Collider other)
    {
        target.localScale = eredetiMeret * osszehuzodas;        
    }

    void OnTriggerExit(Collider other)
    {

        target.localScale = eredetiMeret;
    }

}
```
  - El is tudna ugrani? Véletlenszerű irányba, esetleg a Player-től ellentétesen? Esetleg oszcillál fel, le, ha közel van a Player?   
  - Oszcilláláshoz sin/cos függvény (Time.time az indítás óta eltelt idő):
  
```csharp
using System;

np.x += kiteres * (float) Math.Sin(sebesseg * Time.time);
np.z += kiteres * (float) Math.Cos(sebesseg * Time.time);
```

## Linkek

 * [Unity (personal edition) letöltés](http://unity3d.com/get-unity/download?ref=personal) (Regisztrálni is kell, de ingyenes)
 * [Blender letöltés](https://www.blender.org/download/)
 * [GitHub kliens (későbbre)](https://desktop.github.com/)
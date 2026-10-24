# haz-rl-k

import math


def kontrol_et(lat, lon, nokta):
    try:
        lat = float(lat)
        lon = float(lon)
    except (ValueError, TypeError):
        raise ValueError(
            f"{nokta}: Enlem ve boylam sayı olmalıdır."
        )

    if not math.isfinite(lat) or not math.isfinite(lon):
        raise ValueError(f"{nokta}: Sonlu sayılar girilmelidir.")

    if not -90 <= lat <= 90:
        raise ValueError(f"{nokta}: Enlem -90 ile 90 arasında olmalıdır.")

    if not -180 <= lon <= 180:
        raise ValueError(f"{nokta}: Boylam -180 ile 180 arasında olmalıdır.")

    return lat, lon


# Haversine formülü: mesafe (metre)
def mesafe(lat1, lon1, lat2, lon2):
    R = 6371000

    lat1, lon1, lat2, lon2 = map(
        math.radians, (lat1, lon1, lat2, lon2)
    )

    dlat = lat2 - lat1
    dlon = lon2 - lon1

    a = (
        math.sin(dlat / 2) ** 2
        + math.cos(lat1) * math.cos(lat2)
        * math.sin(dlon / 2) ** 2
    )

    a = max(0.0, min(1.0, a))
    c = 2 * math.atan2(math.sqrt(a), math.sqrt(1 - a))

    return R * c


# Kuzeyden saat yönünde yön (derece)
def yon(lat1, lon1, lat2, lon2):
    lat1, lon1, lat2, lon2 = map(
        math.radians, (lat1, lon1, lat2, lon2)
    )

    dlon = lon2 - lon1

    x = math.sin(dlon) * math.cos(lat2)
    y = (
        math.cos(lat1) * math.sin(lat2)
        - math.sin(lat1) * math.cos(lat2) * math.cos(dlon)
    )

    aci = math.degrees(math.atan2(x, y))
    return (aci + 360) % 360


def rota_hesapla():
    rota = {
        "A": (39.8860, 32.7810),
        "B": (39.8925, 32.7905),
        "C": (39.8800, 32.7950)
    }

    # Koordinatları hesaplamadan önce doğrula
    for isim in rota:
        rota[isim] = kontrol_et(*rota[isim], isim)

    bacaklar = [("A", "B"), ("B", "C")]
    toplam = 0

    print(f"{'Bacak':<12}{'Mesafe (m)':>15}{'Yön (°)':>15}")
    print("-" * 42)

    for baslangic, bitis in bacaklar:
        p1 = rota[baslangic]
        p2 = rota[bitis]

        d = mesafe(*p1, *p2)
        y = yon(*p1, *p2)
        toplam += d

        print(
            f"{baslangic + ' -> ' + bitis:<12}"
            f"{d:>15.2f}{y:>15.2f}"
        )

    print("-" * 42)
    print(f"{'Toplam':<12}{toplam:>15.2f}")


try:
    rota_hesapla()
except ValueError as hata:
    print("Hata:", hata)



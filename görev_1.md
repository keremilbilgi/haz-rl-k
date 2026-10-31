import math

# Dünya'nın ortalama yarıçapı (metre)
R = 6371000


# Koordinat kontrolü
def kontrol(lat, lon):
    lat = float(lat)
    lon = float(lon)

    if lat < -90 or lat > 90:
        raise ValueError("Enlem -90 ile 90 arasında olmalıdır.")

    if lon < -180 or lon > 180:
        raise ValueError("Boylam -180 ile 180 arasında olmalıdır.")

    return lat, lon


# İki koordinat arasındaki mesafe
def mesafe(lat1, lon1, lat2, lon2):

    lat1, lon1 = kontrol(lat1, lon1)
    lat2, lon2 = kontrol(lat2, lon2)

    # Dereceleri radyana çevir
    lat1 = math.radians(lat1)
    lon1 = math.radians(lon1)
    lat2 = math.radians(lat2)
    lon2 = math.radians(lon2)

    dlat = lat2 - lat1
    dlon = lon2 - lon1

    a = (math.sin(dlat / 2) ** 2 +
         math.cos(lat1) * math.cos(lat2) *
         math.sin(dlon / 2) ** 2)

    c = 2 * math.atan2(math.sqrt(a), math.sqrt(1 - a))

    return R * c


# İki koordinat arasındaki yön
def yon(lat1, lon1, lat2, lon2):

    lat1, lon1 = kontrol(lat1, lon1)
    lat2, lon2 = kontrol(lat2, lon2)

    lat1 = math.radians(lat1)
    lon1 = math.radians(lon1)
    lat2 = math.radians(lat2)
    lon2 = math.radians(lon2)

    dlon = lon2 - lon1

    y = math.sin(dlon) * math.cos(lat2)

    x = (math.cos(lat1) * math.sin(lat2) -
         math.sin(lat1) * math.cos(lat2) *
         math.cos(dlon))

    derece = math.degrees(math.atan2(y, x))

    return (derece + 360) % 360


# GPS noktaları
A = (39.8860, 32.7810)
B = (39.8925, 32.7905)
C = (39.8800, 32.7950)


# Rota hesaplama
try:
    AB = mesafe(A[0], A[1], B[0], B[1])
    AB_yon = yon(A[0], A[1], B[0], B[1])

    BC = mesafe(B[0], B[1], C[0], C[1])
    BC_yon = yon(B[0], B[1], C[0], C[1])

    toplam = AB + BC

    print("ROTA TABLOSU")
    print("---------------------------------------")
    print("Rota       Mesafe (m)       Yön (derece)")
    print("---------------------------------------")
    print(f"A -> B     {AB:.2f}           {AB_yon:.2f}")
    print(f"B -> C     {BC:.2f}           {BC_yon:.2f}")
    print("---------------------------------------")
    print(f"Toplam mesafe: {toplam:.2f} metre")

except (ValueError, TypeError) as hata:
    print("Hata:", hata)






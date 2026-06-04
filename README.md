besic pengrograman
# PERTANDINGAN BADMINTON
print("\n=== PERTANDINGAN BADMINTON ===")
a = input("Nama pemain A: ")
b = input("Nama pemain B: ")
ronde = int(input("Jumlah ronde : "))

indonesia = 0
china = 0
penilaian = ronde // 2 + 1

for r in range(1, ronde+1):
    if indonesia == penilaian or china == penilaian:
        break

    print(f"\n-- Ronde {r} --")
    poin_a = int(input(f"  Poin {a}: "))
    poin_b = int(input(f"  Poin {b}: "))

    if poin_a >= 22 and poin_b < 22:
        indonesia += 1
        print(f"  {a} menang ronde ini!")
    elif poin_b >= 22 and poin_a < 22:
        china += 1
        print(f"  {b} menang ronde ini!")
    elif poin_a > poin_b:
        indonesia += 1
        print(f"  {a} unggul ronde ini!")
    elif poin_b > poin_a:
        china += 1
        print(f"  {b} unggul ronde ini!")
    else:
        print("  Ronde ini seri!")

    print(f"  Skor: {a} {indonesia} - {china} {b}")

# HASIL AKHIR
print("\n=== HASIL AKHIR ===")
print(f"{a}: {indonesia} ronde  |  {b}: {china} ronde")
if indonesia > china:
    print(f"PEMENANG: {a}!")
elif indonesia > china:
    print(f"PEMENANG: {b}!")
else:
    print("Hasil: SERI!")


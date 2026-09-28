# CAPSTONE-Tiplashtirilgan-Vazifalar-repozitoriyasi\
TypeScript
// ==========================================
// 1. Asosiy Interfeyslar va Turlar
// ==========================================

// Vazifa holati uchun literal type union
type VazifaHolati = "kutilmoqda" | "jarayonda" | "bajarildi";
type MuhimlikDarajasi = "past" | "orta" | "yuqori";

interface Vazifa {
    id: number;
    sarlavha: string;
    holat: VazifaHolati;
    muhimlik: MuhimlikDarajasi;
}

// Utility types yordamida DTO'larni hosil qilish
type VazifaYaratishDTO = Omit<Vazifa, "id">;
type VazifaYangilashDTO = Partial<VazifaYaratishDTO>;


// ==========================================
// 2. Generic Repozitoriy Interfeysi
// ==========================================
interface Repozitoriy<T extends { id: number }> {
    hamma(): T[];
    topish(id: number): T | undefined;
    qoshish(item: Omit<T, "id">): T;
    yangilash(id: number, item: Partial<Omit<T, "id">>): T | undefined;
    ochirish(id: number): boolean;
}


// ==========================================
// 3. VazifaRepozitoriyi Class'i
// ==========================================
class VazifaRepozitoriyi implements Repozitoriy<Vazifa> {
    private vazifalar: Vazifa[] = [];
    private keyingiId: number = 1;

    hamma(): Vazifa[] {
        return this.vazifalar;
    }

    topish(id: number): Vazifa | undefined {
        return this.vazifalar.find(v => v.id === id);
    }

    qoshish(item: VazifaYaratishDTO): Vazifa {
        const yangiVazifa: Vazifa = {
            id: this.keyingiId++,
            ...item
        };
        this.vazifalar.push(yangiVazifa);
        return yangiVazifa;
    }

    yangilash(id: number, item: VazifaYangilashDTO): Vazifa | undefined {
        const mavjudVazifa = this.topish(id);
        if (!mavjudVazifa) return undefined;

        // Ma'lumotlarni yangilash
        if (item.sarlavha !== undefined) mavjudVazifa.sarlavha = item.sarlavha;
        if (item.holat !== undefined) mavjudVazifa.holat = item.holat;
        if (item.muhimlik !== undefined) mavjudVazifa.muhimlik = item.muhimlik;

        return mavjudVazifa;
    }

    ochirish(id: number): boolean {
        const indeks = this.vazifalar.findIndex(v => v.id === id);
        if (indeks === -1) return false;
        
        this.vazifalar.splice(indeks, 1);
        return true;
    }
}


// ==========================================
// 4. CRUD amallarini sinab ko'rish
// ==========================================
const repo = new VazifaRepozitoriyi();

// 1. Qo'shish (Create)
console.log("--- VAZIFALAR QO'SHILMOqda ---");
const v1 = repo.qoshish({ sarlavha: "TypeScript'ni o'rganish", holat: "jarayonda", muhimlik: "yuqori" });
const v2 = repo.qoshish({ sarlavha: "Uy tozalash", holat: "kutilmoqda", muhimlik: "orta" });
console.log(repo.hamma());

// 2. Topish (Read)
console.log("\n--- ID BO'YICHA TOPISH ---");
console.log(repo.topish(1));

// 3. Yangilash (Update)
console.log("\n--- VAZIFANI YANGILASH ---");
repo.yangilash(1, { holat: "bajarildi" });
console.log(repo.topish(1));

// 4. O'chirish (Delete)
console.log("\n--- VAZIFANI O'CHIRISH ---");
repo.ochirish(2);
console.log("Qolgan vazifalar:", repo.hamma());
Loyihaning qisqacha tuzilishi va afzalliklari:
To'liq tur xavfsizligi (Type Safety): Literal turlar (VazifaHolati, MuhimlikDarajasi) noto'g'ri qiymatlar yozilishining oldini oladi.

Utility Types: Omit orqali ID majburiy bo'lmagan yaratish DTO'si, Partial orqali esa xossalari ixtiyoriy bo'lgan yangilash DTO'si yaratildi.

Inkapsulyatsiya: private kalit so'zi orqali ichki massiv tashqi aralashuvdan himoya qilindi.

Generics & Interface: Repozitoriy<T> interfeysi istalgan boshqa tur uchun ham qayta ishlatish imkonini beradi.

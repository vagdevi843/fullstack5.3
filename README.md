# fullstack5.3
// ecommerceModel.js
const mongoose = require("mongoose");

// 1️⃣ CONNECT TO MONGODB
mongoose
  .connect("mongodb://127.0.0.1:27017/ecommerceDB")
  .then(() => console.log("✅ Connected to MongoDB"))
  .catch((err) => console.error("❌ Connection Error:", err));

// 2️⃣ DEFINE NESTED SCHEMA FOR VARIANTS
const variantSchema = new mongoose.Schema({
  color: String,
  size: String,
  stock: Number,
  price: Number,
});

// 3️⃣ DEFINE PRODUCT SCHEMA (Nested Variants)
const productSchema = new mongoose.Schema({
  name: { type: String, required: true },
  description: String,
  brand: String,
  basePrice: Number,
  variants: [variantSchema], // <-- Nested array of subdocuments
});

// 4️⃣ DEFINE CATEGORY SCHEMA (Nested Products)
const categorySchema = new mongoose.Schema({
  categoryName: { type: String, required: true },
  description: String,
  products: [productSchema], // <-- Nested array of products
});

// 5️⃣ CREATE MODEL
const Category = mongoose.model("Category", categorySchema);

// 6️⃣ INSERT SAMPLE DATA
async function insertSampleData() {
  await Category.deleteMany(); // Clean collection before inserting

  const electronics = new Category({
    categoryName: "Electronics",
    description: "Smartphones, Laptops, and Accessories",
    products: [
      {
        name: "iPhone 15 Pro",
        description: "Apple smartphone with A17 chip",
        brand: "Apple",
        basePrice: 1200,
        variants: [
          { color: "Black", size: "128GB", stock: 20, price: 1200 },
          { color: "Silver", size: "256GB", stock: 15, price: 1350 },
        ],
      },
      {
        name: "Dell XPS 13",
        description: "Premium ultrabook laptop",
        brand: "Dell",
        basePrice: 1400,
        variants: [
          { color: "Silver", size: "16GB RAM", stock: 10, price: 1400 },
          { color: "Black", size: "32GB RAM", stock: 5, price: 1600 },
        ],
      },
    ],
  });

  await electronics.save();
  console.log("✅ Sample data inserted!");
}

// 7️⃣ QUERY EXAMPLES
async function displayCatalog() {
  const catalog = await Category.find();
  console.log("\n🛒 E-Commerce Catalog:\n");
  catalog.forEach((cat) => {
    console.log(`Category: ${cat.categoryName}`);
    cat.products.forEach((p) => {
      console.log(`  • ${p.name} (${p.brand}) - $${p.basePrice}`);
      p.variants.forEach((v) => {
        console.log(
          `     → ${v.color}, ${v.size} — Stock: ${v.stock}, Price: $${v.price}`
        );
      });
    });
  });
}

// 8️⃣ MAIN FUNCTION
(async () => {
  await insertSampleData();
  await displayCatalog();
  mongoose.connection.close();
})();

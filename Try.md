Excellent catch — you're absolutely right. In Azure Data Factory's Flatten transformation, the Unroll root is an input field, not a checkbox — and it accepts a column name. Let's go over what that really means.


---

✅ What is Unroll root (input field) in Flatten?

The Unroll root input field is used when your data contains multiple nested arrays (or complex types), and you want to flatten an array that is not at the top level of the structure.

In simple terms:

> Unroll root tells ADF which column (i.e., nested object or array) to treat as the "starting point" for flattening — even if it's buried deeper in the hierarchy.




---

📌 Why is this needed?

Normally, Flatten works on top-level arrays. But if the array you want to flatten is inside a child object, or inside another array of objects, you need to navigate to it explicitly — and that’s where Unroll root comes in.


---

🧪 Practical Example:

🎯 JSON Example:

[
  {
    "id": 1,
    "name": "Sourav",
    "address": {
      "city": "Delhi",
      "phones": [
        { "type": "home", "number": "111" },
        { "type": "work", "number": "222" }
      ]
    }
  }
]


---

🧱 Goal:

You want to flatten the phones array, which is nested inside address.


---

✅ How to Use Unroll root:

In the Flatten transformation:

Unroll root = address.phones

Unroll by = Leave empty (or set to individual array elements if needed)



---

✅ Output:

id	name	address.city	type	number

1	Sourav	Delhi	home	111
1	Sourav	Delhi	work	222


Here, ADF:

Navigates to address.phones

Flattens that array (even though it's not at the root level)

And pulls the fields type, number, and keeps root values like id, name, etc.



---

🛠 Without Unroll root:

If you tried to use Flatten without setting Unroll root, and your array (phones) is nested, ADF wouldn’t know where to look — you’d get either:

An empty output

Or no flattening effect



---

💡 When to Use Unroll root

Scenario	Example

Flattening array nested inside an object	address.phones
Flattening array nested multiple levels deep	orders.items.products
Flattening not directly accessible arrays	Use path in Unroll root



---

⚠️ Gotchas:

The path in Unroll root must be valid and point to an array.

If Unroll root is incorrect or points to a non-array, the Flatten will either do nothing or error out.



---

Would you like a sample JSON + mapping data flow template to try this out directly in ADF UI?

<br>
<br>
<br>

Perfect — let's go into full hands-on mode and walk through a working ADF Flatten transformation example with and without Unroll root using realistic nested JSON data. Since you're absolutely right that Unroll by is mandatory, I’ll include that properly.


---

✅ Scenario Setup:

You're working with the following nested JSON structure:

[
  {
    "orderId": 1001,
    "customer": {
      "name": "Sourav",
      "contacts": [
        { "type": "email", "value": "sourav@example.com" },
        { "type": "phone", "value": "1234567890" }
      ]
    }
  },
  {
    "orderId": 1002,
    "customer": {
      "name": "Ananta",
      "contacts": [
        { "type": "email", "value": "ananta@example.com" }
      ]
    }
  }
]

🧾 Objective:

Flatten the contacts array under customer, so each contact (email/phone) becomes its own row, while keeping orderId and customer.name.


---

✅ WITH Unroll root

🔧 Flatten Transformation Settings:

Field	Value

Unroll root	customer.contacts
Unroll by	item (or whatever name you give to individual contact entries)


> ADF will iterate over the contacts array and output one row per contact.




---

🧾 Output (flattened):

orderId	customer.name	type	value

1001	Sourav	email	sourav@example.com
1001	Sourav	phone	1234567890
1002	Ananta	email	ananta@example.com


> ✅ This is the correct and desired output — each contact becomes a row.




---

🚫 WITHOUT Unroll root

🔧 Flatten Transformation Settings:

Field	Value

Unroll root	leave blank
Unroll by	You will not see customer.contacts here!


> ADF doesn’t know where the array is — it expects the array at the root level, but our array is inside customer.




---

🧾 Output:

orderId	customer

1001	{...}
1002	{...}


> ❌ No flattening occurs. You’ll either get nested JSON or a failure depending on your sink or preview.




---

🛠 How to Try This Practically:

1. Create a new Mapping Data Flow


2. Add a Source → point it to a JSON file with the above structure


3. Add a Flatten transformation


4. In Flatten:

Set Unroll root = customer.contacts

Set Unroll by = item



5. Add a Derived Column (optional) to pull item.type, item.value, customer.name, etc.


6. Add a Sink or Data Preview




---

✅ Summary:

Setting	Behavior

Unroll root = customer.contacts<br>Unroll by = item	Flattens contacts inside each customer
Unroll root blank	Doesn’t work, because contacts are nested and not at root



---

Would you like a JSON test file and a complete ADF Data Flow JSON template to import and try it immediately?


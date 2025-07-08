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


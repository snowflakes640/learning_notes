These are things I keep forgetting about

---
### Appendix
1. **Context**
🧠 Think of `context` as a **dictionary** (a bag of variables) that Django hands over to your template.
It lets you send data from your **Python view** to your **HTML template**.
 🔧 Example:
 In your view:
 ```python
 
 def my_view(request):
    context = {
        "name": "Milk",
        "quantity": 2,
        "unit": "Litre"
    }
    return render(request, "inventory/example.html", context)
```

 In your template (`example.html`):
```html
<p>{{ name }} - {{ quantity }} {{ unit }}</p>
```
 
 🧾 What’s happening?
1. You define variables (`name`, `quantity`, `unit`) in your **view**.
2. You put them into the `context` dictionary.
3. Django passes them to your **template**.    
4. In the template, `{{ variable_name }}` pulls it out of the context.

so i can access all the keys in the context from my template simply using the key name?

Exactly! ✅
In Django templates, **every key you put in the `context` dictionary** becomes a variable you can directly use in your template — just by referencing its **key name** with double curly braces: `{{ key_name }}`.
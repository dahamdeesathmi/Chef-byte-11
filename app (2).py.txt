import streamlit as st
from groq import Groq

client = Groq(api_key=st.secrets["GROQ_API_KEY"])

st.set_page_config(page_title="Chef Byte", page_icon="👨‍🍳", layout="centered")

st.markdown("<h1 style='text-align:center;background:linear-gradient(135deg,#FF6B35,#FF4D8D,#845EC2);-webkit-background-clip:text;-webkit-text-fill-color:transparent'>Chef Byte 👨‍🍳</h1>", unsafe_allow_html=True)
st.markdown("<p style='text-align:center;color:#777;font-weight:600'>Toss in your ingredients - I will cook up a recipe!</p>", unsafe_allow_html=True)
st.markdown("---")

ingredients_input = st.text_input("Your Ingredients", placeholder="e.g. eggs, tomato, garlic")

col1, col2 = st.columns(2)
with col1:
    cuisine = st.selectbox("Cuisine", ["Any cuisine","Italian","Asian","Mexican","Indian","Mediterranean","American"])
with col2:
    diet = st.selectbox("Diet", ["Any diet","Vegetarian","Vegan","Gluten-Free","High Protein","Low Carb"])

if st.button("Cook Up a Recipe!"):
    if not ingredients_input.strip():
        st.error("Please add at least one ingredient first!")
    else:
        ingredients = [i.strip().capitalize() for i in ingredients_input.split(",") if i.strip()]
        cuisine_text = f" The cuisine style should be {cuisine}." if cuisine != "Any cuisine" else ""
        diet_text = f" The recipe must be {diet}." if diet != "Any diet" else ""
        prompt = f"You are Chef Byte, a fun and enthusiastic AI chef! The user has these ingredients: {', '.join(ingredients)}.{cuisine_text}{diet_text} Suggest ONE creative and delicious recipe. Format like this: RECIPE NAME: [name], Time: [time], Serves: [number], INGREDIENTS: - [each with quantity], INSTRUCTIONS: 1.[step] 2.[step], CHEF TIP: [one tip]. Keep it friendly and fun!"

        with st.spinner("Chef Byte is cooking... please wait!"):
            try:
                response = client.chat.completions.create(
                    model="llama-3.3-70b-versatile",
                    messages=[{"role": "user", "content": prompt}],
                    max_tokens=1000
                )
                recipe = response.choices[0].message.content
                st.success("Your recipe is ready!")
                st.markdown(f"<div style='background:linear-gradient(135deg,#fff8f0,#fff0f8);border:2px solid #ffd0a0;border-radius:20px;padding:24px;white-space:pre-wrap;font-family:Georgia,serif;line-height:1.8'>{recipe}</div>", unsafe_allow_html=True)
            except Exception as e:
                st.error(f"Error: {str(e)}")

st.markdown("---")
st.markdown("<p style='text-align:center;color:#aaa;font-size:0.85rem'>Made with love by Chef Byte</p>", unsafe_allow_html=True)

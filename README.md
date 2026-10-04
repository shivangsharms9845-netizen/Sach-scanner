# Sach-scanner
# pip install streamlit requests
import streamlit as st
import requests

st.set_page_config(page_title="Sach Scanner - Asli Wala")
st.title("🔍 Sach Scanner - Asli AI")

API_KEY = "YAHAN_APNI_GOOGLE_API_KEY_PASTE_KARO" # <-- Yahan key dalo

khabar = st.text_area("WhatsApp Forward Yaha Paste Karo:", height=150)

if st.button("Asli Sach Check Karo"):
    if not khabar:
        st.warning("Pehle khabar to likho bhai")
    else:
        with st.spinner("Google + PIB ka pura database check ho raha hai..."):
            url = f"https://factchecktools.googleapis.com/v1alpha1/claims:search?query={khabar}&key={API_KEY}&languageCode=hi"

            try:
                res = requests.get(url).json()

                if "claims" in res and len(res["claims"]) > 0:
                    claim = res["claims"][0]
                    review = claim["claimReview"][0]

                    rating = review["textualRating"] # Jaise: False, Misleading
                    publisher = review["publisher"]["name"] # Jaise: PIB, AltNews

                    if "false" in rating.lower() or "fake" in rating.lower():
                        st.error(f"🔴 {rating.upper()} - 100% JHOOTH HAI")
                    else:
                        st.warning(f"🟡 {rating.upper()} - GHUMA KE BOLA GAYA HAI")

                    st.write(f"**Sach kya hai:** {claim['text']}")
                    st.write(f"**Kisne check kiya:** {publisher}")
                    st.write(f"**Source Link:** {review['url']}")

                else:
                    st.success("🟢 Abhi tak is khabar ko kisi ne Fake nahi bataya hai, shayad sahi hai.")
                    st.info("Tip: Fir bhi pibfactcheck.gov.in pe ek baar search kar lo.")

            except Exception as e:
                st.error(f"Error: {e}. API Key sahi dala hai na?")

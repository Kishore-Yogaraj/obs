### The MVP
The **Minimum Viable Product (MVP)** for **Savor** is a mobile app designed to connect consumers with nearby restaurant deals while allowing restaurant owners to easily manage and promote their offers. The MVP will focus on two primary user groups: consumers looking for discounts and restaurant managers who want to attract more customers with promotions. Users will be able to browse local deals through both a list and an interactive map view, filtering results based on food type, price, discount level, or dietary preferences. Each deal will include essential details such as a description, discount percentage, expiration date, and redemption method. To make deal discovery more engaging, the app will feature a Google Maps integration that visually displays restaurant promotions, allowing users to click on individual locations for more information.

Restaurant managers will have access to a simple admin panel where they can log in and create, edit, or remove deals. They will be able to set specific deal conditions, such as time-limited offers or first-come-first-serve discounts, and upload images and descriptions to attract customers. To provide basic insights, the MVP will include a simple engagement tracking system that allows restaurants to see how many users have viewed and redeemed their deals. Additionally, consumers will have the option to receive push notifications when new deals are added or when a nearby offer is about to expire, ensuring they never miss out on a promotion.

The technical foundation of the MVP will be built using a cross-platform mobile framework like React Native or Flutter, with Firebase or AWS handling backend services such as user authentication, database management, and push notifications. A PostgreSQL or Firestore database will store restaurant deals and user preferences, while Google Maps API will power the interactive map functionality.

While the MVP will focus on delivering core deal discovery and management features, more advanced capabilities—such as AI-driven deal recommendations, loyalty program integration, in-app purchases, and advanced analytics—will be planned for future releases. The goal of the MVP is to validate the demand for a centralized restaurant deals platform by measuring key metrics such as the number of restaurants onboarded, active deals, user engagement, and deal redemption rates. The next steps in development will include wireframing the UI/UX, setting up the backend infrastructure, implementing the restaurant admin panel, building the consumer-facing features, and conducting beta testing with a select group of restaurants and users.


# Savor – Product Requirements Document (PRD)

## 1. Overview

**Savor** is a mobile app designed to help users find restaurant deals and specials in their area. The app will also allow restaurant managers/owners to post and manage deals. The goal is to provide a centralized platform where consumers can easily discover promotions that are otherwise difficult to find, while helping restaurants optimize and improve deal performance.

- Focus on business application
	- Business strategy
	- B2B and B2C
	- **At core pitch as B2B**
		- optimizing deals for businesses
		- **Market ourselves as deal management company**
		- Think of Uber
	- B2C
		- Byproduct of B2B
- Target a large market
- Maximize Toronto (local)

---

## 2. Problem Statement

Many university students and budget-conscious consumers miss out on great restaurant deals because they are only available on individual restaurant websites, physical store windows, or other hard-to-find locations. 

**Primary**
Restaurants, on the other hand, lack a data-driven way to understand how their deals are performing. **Savor** solves this by finding deals in a user-friendly app and, in future iterations, providing AI-driven deal recommendations for restaurant owners.

---

## 3. Target Audience

- **Seconday Users**: University students, budget-conscious consumers, and food enthusiasts looking for discounts on dining options.
- **Primary Users**: Restaurant owners and managers who want to promote deals and analyze their performance.

---

## 4. Core Features (MVP Features)

### 4.1 Consumer Features
1. **Deals Discovery**
    - Users can view all nearby restaurant deals based on location.
    - Deals are displayed in a **list view and interactive map view**.
    - Filtering options by **food type, price, discount level, and dietary preference**.
2. **Interactive Map Interface**
    - Users can see restaurant promotions plotted on a map.
    - Tap on a deal to see details like discount amount, validity period, and redemption instructions.
3. **Deal Details & Redemption** (Have ready to go for pitches not directly consumer facing) 
    - Users can view deal descriptions, images, and terms (e.g., “Buy 1 Get 1 Free”).
    - QR code or **digital coupon system** for deal redemption.
4. **User Account & Preferences**
    - Users can save favorite restaurants and get **personalized notifications** about deals.
    - Optional email or phone number sign-up for exclusive discounts.
5. **Push Notifications**
    - Users can receive alerts about:
        - New deals at their favorite restaurants.
        - Expiring promotions nearby.
### 4.2 Restaurant Owner Features
1. **Admin Panel for Deal Management**
    - Restaurant managers can **post new deals**, edit existing deals, or remove expired ones.
    - Set deal timeframes, discounts, and special conditions (e.g., “Only valid after 5 PM”).
2. **Deal Analytics Dashboard** _(Future AI Feature Placeholder)_
    - Restaurants can **track engagement metrics** (e.g., number of views, redemptions).
    - Basic version for MVP: Displays number of users who clicked on a deal.

### 4.3 Future Expansion (Post-MVP Features)
- **AI-Driven Deal Optimization**:
    - Recommends deal strategies based on historical performance and competitor trends.
    - Suggests discount levels to maximize engagement.
- **Loyalty Program Integration**:
    - Users can link existing restaurant loyalty programs to **store and redeem rewards**.
- **In-App Purchases & Reservations**:
    - Users can pre-purchase deals or reserve a table through the app.
- **Cash Discount Indicators**:
    - Highlights restaurants offering extra savings for **cash payments**.

---

## 5. Technology Stack & Development Considerations

### 5.1 Mobile App Development
- **Platform**: iOS & Android (React Native or Flutter recommended for cross-platform compatibility).
- **Backend**: Firebase or AWS (for authentication, database, and push notifications).
- **Database**: PostgreSQL or Firebase Firestore for storing deals and user preferences.
- **Map API**: Google Maps API for displaying deal locations or mapbox
- Will finalize this officially tonight

### 5.2 AI Feature Considerations _(Future Expansion)_
- **Machine Learning Model**: Analyze engagement patterns to suggest optimal deal types.
- **Data Collection**: Gather anonymized user interactions with deals.

---

## 6. MVP Scope & Prioritization

### High-Priority (Must-Have for MVP)

✅ Restaurant managers can **add, edit, and remove deals**.  
✅ Users can **view deals in list and map format**.  
✅ Basic **filtering options** (food type, price, dietary preferences).  
✅ **Push notifications** for expiring deals and new offers.  
✅ **QR Code/Digital Coupon** for redemption.

- QR code to landing page
### Medium-Priority (Consider if Time Allows)
🔶 User accounts with saved preferences.  
🔶 Basic **deal engagement tracking** (clicks, views).
### Low-Priority (Post-MVP Enhancements)
🔹 AI-powered deal recommendations.  
🔹 Loyalty program integration.  
🔹 In-app purchases & table reservations.

---

## 7. Success Metrics for MVP

- Number of restaurants signed up within the first **3 months**.
- Number of **deals posted** and engaged with by users.
- User engagement: Daily/weekly active users.
- **Deal redemption rate** via QR code or digital coupon.

---

## 8. Conclusion & Next Steps
This PRD outlines the vision and MVP for **Savor**, with a focus on connecting users with restaurant deals and providing restaurant owners a seamless way to manage offers. The **next steps** involve:
- Wireframing the UI/UX.
- Choosing the **tech stack**.
- Developing a working prototype.
- Beta testing with early adopters.






# Example Task Packet

GOAL:
- Add social login support to the mobile app while keeping Cognito as the auth source of truth

CONSTRAINTS:
- Backend must remain unchanged
- Existing JWT validation must continue working
- Output must be compact

KNOWN FACTS:
- Web app already uses Cognito
- API validates JWTs through Cognito JWKS
- Mobile app is being built with Expo

INPUT ARTIFACTS:
- mobile/package.json
- web auth notes
- src/auth/*
- docs/mobile-auth.md

TASK:
- Identify the smallest viable integration approach and the key implementation steps

RETURN CONTRACT:
- shared compact contract only
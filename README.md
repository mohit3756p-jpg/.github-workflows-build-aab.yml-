# .github-workflows-build-aab.yml-
github
name: Build AAB

on:
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Set up Gradle
        uses: gradle/actions/setup-gradle@v6
        with:
          gradle-version: '8.9'

      - name: Set up Android SDK
        uses: android-actions/setup-android@v3

      - name: Install Android SDK 36
        run: |
          yes | sdkmanager --licenses || true
          sdkmanager "platforms;android-36" "build-tools;36.0.0"

      - name: Build Release AAB
        run: |
          gradle :app:bundleRelease
        env:
          ALPHA_UPLOAD_KEYSTORE: ${{ github.workspace }}/alpha-upload-key.jks
          ALPHA_UPLOAD_STORE_PASSWORD: ${{ secrets.ALPHA_UPLOAD_STORE_PASSWORD }}
          ALPHA_UPLOAD_KEY_PASSWORD: ${{ secrets.ALPHA_UPLOAD_KEY_PASSWORD }}

      - name: Upload AAB
        uses: actions/upload-artifact@v4
        with:
          name: Alpha-Music-App-AAB
          path: app/build/outputs/bundle/release/*.aab

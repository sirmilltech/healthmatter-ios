# healthmatter-ios
Health Matter APP


workflows:
  ios-release:
    name: iOS App Store Release
    instance_type: mac_mini_m2

    environment:
      vars:
        BUNDLE_ID: "com.despia.healthmatterhm"
        APP_STORE_APP_ID: 1570610860
        XCODE_WORKSPACE: "swiftly.xcworkspace"
        XCODE_SCHEME: "swiftly"

    scripts:
      - name: Initialize keychain
        script: |
          keychain initialize

      - name: Fetch signing files
        script: |
          app-store-connect fetch-signing-files \
          "$BUNDLE_ID" \
          --type IOS_APP_STORE \
          --create

      - name: Import certificates
        script: |
          keychain add-certificates

      - name: Apply signing
        script: |
          xcode-project use-profiles

      - name: Build IPA
        script: |
          xcode-project build-ipa \
            --workspace "$XCODE_WORKSPACE" \
            --scheme "$XCODE_SCHEME"

    artifacts:
      - build/ios/ipa/*.ipa

    publishing:
      app_store_connect:
        api_key: $APP_STORE_CONNECT_PRIVATE_KEY
        key_id: $APP_STORE_CONNECT_KEY_IDENTIFIER
        issuer_id: $APP_STORE_CONNECT_ISSUER_ID

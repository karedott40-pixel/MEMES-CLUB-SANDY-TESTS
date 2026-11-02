# Sandy: controlled build and mathematical regression tests

Technical repair experiment based on iq/sandy at b3dacdff5ec851c4bcc98af89ceff8b227c5c19a (MIT).

The original mathematical search can overflow the stack for an adjacent interval. A separate bounded-search repair passes eight focused tests, including exhaustive enumeration for 540 parameter combinations. The existing fixed-fee model is preserved and is not verified against current live pools.

The manually triggered GitHub workflow verifies the frozen upstream archive, applies only this mathematical change, and attempts to compile the workspace. It never launches the bot, creates a wallet, signs a trade, or deploys a program. No cache or artifact upload is enabled. A successful build does not establish a functional or profitable trading system.

The original source and its license are preserved. This is a technical test repository, not a ready-to-use trading product.

The source files are in SANDY_TEST_SOURCES.zip; the workflow extracts the archive before testing.

#!/usr/bin/env python3
"""
Safe OR28 (refund) access check. Sends a refund for an order line that does not exist,
so it can never refund anyone. Only the HTTP status tells us whether the login is accepted.
Env: MIRAKL_API_KEY (required), MIRAKL_HOST (optional)
"""
import os
import sys

import requests

HOST = os.getenv("MIRAKL_HOST", "https://kmartau-prod.mirakl.net").rstrip("/")
KEY = os.getenv("MIRAKL_API_KEY") or sys.exit("[FATAL] Missing env var: MIRAKL_API_KEY")
FAKE = {"refunds": [{"order_line_id": "ZZ-ACCESS-TEST-NOT-A-REAL-LINE", "amount": 0.01,
                     "shipping_amount": 0, "quantity": 1, "reason_code": "TEST"}]}


def verdict(code: int) -> str:
    if code in (401, 403):
        return "LOGIN NOT ACCEPTED for refunds"
    if code in (400, 404, 405, 422):
        return "login ACCEPTED (only the fake data was rejected)"
    if 200 <= code < 300:
        return "UNEXPECTED success - check the Kmart portal for any refund created by this test"
    return "inconclusive"


for label, auth in (("shop API key as-is", KEY), ("shop API key as Bearer", f"Bearer {KEY}")):
    r = requests.put(f"{HOST}/api/orders/refund", json=FAKE, timeout=60,
                     headers={"Authorization": auth, "Content-Type": "application/json", "Accept": "application/json"})
    print(f"[{label}] HTTP {r.status_code} -> {verdict(r.status_code)}")
    print(f"    response: {r.text[:300]}")

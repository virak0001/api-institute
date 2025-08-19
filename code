watch(
  models,
  () => {
    models.value.forEach((contact: ItemInterface, index: number) => {
      processContact(contact, index)
    })
  },
  {
    deep: true,
    immediate: true,
  }
)

// -------------------- Functions --------------------

function processContact(contact: ItemInterface, index: number) {
  isCorrectRegExp(contact)

  if (contact.phone) {
    formatPhone(contact)
  } else {
    applyDefaultPhoneSettings(contact)
  }

  if (contact.phone) {
    validatePhoneNumber(contact, index)
  }

  onWatch(contact.phone, `contacts.${index}.phone`, props.rule)
}

function formatPhone(contact: ItemInterface) {
  const parsed = parsePhoneNumberFromString(contact.phone, { extract: true })

  const prefixTel = parsed
    ? parsed.number
    : `+${contact.countryCallingCode || 855} ${contact.phone}`

  const parsedTel = parsePhoneNumberFromString(prefixTel)

  if (parsed && parsedTel && parsed.country) {
    contact['placeholder'] =
      parsed.countryCallingCode === '855'
        ? '## ### ####'
        : telPlaceholder(parsed.country)

    applyMask(contact, parsedTel.nationalNumber, parsed.countryCallingCode)
  } else if (!parsed && parsedTel) {
    applyMask(contact, parsedTel.nationalNumber)
  }
}

function applyDefaultPhoneSettings(contact: ItemInterface) {
  contact['countryCallingCode'] = contact['countryCallingCode'] ?? 855

  const countryCode = findCountryCode(
    contact['countryCallingCode'] as number
  ) as CountryCallingCode

  contact['placeholder'] =
    contact['countryCallingCode'] === 855
      ? props.placeholder
      : telPlaceholder(countryCode as any)
}

function validatePhoneNumber(contact: ItemInterface, index: number) {
  const isValid = isValidPhoneNumberForCountry(
    `${contact.phone}`,
    findCountryCode(contact['countryCallingCode'] as number) as CountryCode
  )

  const key = `contacts-${index}-phone`

  if (!isValid) {
    $errors.fill({ [key]: ['Phone Number is invalid'], ...errors.all() })
  } else {
    $errors.clear(key)
  }
}

function applyMask(
  contact: ItemInterface,
  nationalNumber: string,
  callingCode?: string | number
) {
  const mask = new Mask({ mask: contact['placeholder'] })
  contact.phone = mask.masked(nationalNumber)

  if (callingCode) {
    contact['countryCallingCode'] = Number(callingCode)
  }
}

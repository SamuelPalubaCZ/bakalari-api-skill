# Campaign Module (`https://campaign.bakalari.cz`)

## Overview

The campaign module provides access to the school's information channel, advertisements, and announcements (informační kanál). This is a separate service from the main API.

## Endpoint

### Banner Information
```
GET https://campaign.bakalari.cz/bannerinfo/{Location}/{CampaignCategoryCode}
```

Returns campaign banners and advertisements for the specified location and category.

**Authentication**: Not required (public endpoint)

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `Location` | string | Platform and placement type |
| `CampaignCategoryCode` | string | Campaign category from user module |

### Location Values

| Value | Description |
|-------|-------------|
| `mobileapp` | Mobile application |
| `LargeBanner` | Large banner placement |
| `SmallBannersPanel` | Small banners panel |

## Response Structure

```json
{
  "banners": [
    {
      "messageId": 1049,
      "imageUrl": "https://www.bakalari.cz/images/kampane/NadaceAlbatros-TalentifyMe-Rodic-340x330.jpg",
      "linkUrl": "https://www.talentify.me/cs-cz",
      "priority": 50
    }
  ]
}
```

## Response Fields

### Banner Object

| Field | Type | Description |
|-------|------|-------------|
| `messageId` | number | Unique message identifier |
| `imageUrl` | string | Banner image URL |
| `linkUrl` | string | Destination link URL |
| `priority` | number | Display priority (higher = more important) |

## Campaign Category Code

The `CampaignCategoryCode` is obtained from the user module response:

```json
// GET /api/3/user response
{
  "CampaignCategoryCode": "STUDENT_PRIMARY"
}
```

Category codes may include:
- `STUDENT_PRIMARY` - Primary school students
- `STUDENT_SECONDARY` - Secondary school students
- `TEACHER` - Teachers
- `PARENT` - Parents

## Usage Example

```http
GET https://campaign.bakalari.cz/bannerinfo/mobileapp/STUDENT_PRIMARY HTTP/1.1
```

## Error Handling

### 500 Internal Server Error (Invalid Category)

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.6.1",
  "title": "An error occured while processing your request.",
  "status": 500,
  "detail": "Invalid category",
  "traceId": "00-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx-xxxxxxxxxxxxxxxx-00"
}
```

Occurs when `CampaignCategoryCode` is invalid or not recognized.

## Display Considerations

1. **Priority ordering** - Display higher priority banners first
2. **Image loading** - Load banner images asynchronously
3. **Link handling** - Open links in appropriate target
4. **Empty state** - Handle no banners gracefully
5. **Caching** - Cache banner data for performance

## Common Use Cases

1. **School announcements** - Official school communications
2. **Event advertisements** - School events and activities
3. **Third-party ads** - Educational partner advertisements
4. **Information display** - General announcements

## Caching Strategy

- **Banner data**: Cache for 1 hour
- **Images**: Cache according to HTTP headers
- **No authentication**: Safe to cache aggressively

## Notes

- This is a separate service from the main Bakalari API
- No authentication required (public endpoint)
- Hosted on `campaign.bakalari.cz` domain
- Campaign category code comes from `/api/3/user` response
- Banners may include school announcements and third-party content
- Priority determines display order
- Not all schools may have active campaigns

## Related Modules

- **User** (`/api/3/user`) - Get CampaignCategoryCode
- **Events** (`/api/3/events`) - Official school events

## Technical Details

### Campaign System

The campaign system:
- Provides targeted announcements to different user groups
- Supports image-based advertisements
- Tracks engagement (via messageId)
- Separate from main API for performance
- May include external content (ads, partnerships)

### Integration Steps

1. **Get category code**: Call `/api/3/user` and extract `CampaignCategoryCode`
2. **Request banners**: Call campaign endpoint with location and category
3. **Display banners**: Show in appropriate location based on priority
4. **Handle interactions**: Track clicks and impressions (if required)
